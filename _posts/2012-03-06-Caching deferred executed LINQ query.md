---
title: Caching deferred executed LINQ query
tags: [".Net",  "C#", "LINQ", "Performance", "Cache"]
---
When using LINQ extensively you'll eventually encounter the benefits and the cruelties of deferred execution. When not used correctly it can have a serious performance impact. In this blog I will give a method that combines the benefits of deferred execution with the safety of direct execution.
<!--more-->
Lets take a closer look at deferred execution. It means that a LINQ query is not executed when you instantiate it, but is executed when you iterate over the produced `IEnumerable`. Under the hood the LINQ query creates a wrapper object that holds all information you defined in your query. It also implements `IEnumerable` so it can be used in foreach-loops and other LINQ expressions.

When you loop over the wrapper `IEnumerable` the actual LINQ logic is executed and then the code, e.g. where, order, grouping etc., you have defined in your query is applied. It also means that the query is executed every time you iterate over the wrapper. If you are ordering a collection of a few thousand items this can cause a serious performance penalty, for example.

There are methods to 'disable' deferred execution. Each `IEnumerable` has the `ToList` and `ToArray` extension-methods. These methods cause that the query is executed and produces a static list. After that the query won't be executed again. Yet, this can have some performance impact as well. When we execute the query and never use the result, the query is executed for nothing. This might be contradictory, but will become clear in the following example.

The following codes defines two LINQ queries. The first query is used in the loop, the second query is used as lookup by use of the `FirstOrDefault` method.

```csharp
var appointments = from a in dataStore
                   where a.StartDate < DateTime.Now
                   select a;

var appointmentsFromCom = from ac in comObject
                          where ac.StartDate < DateTime.Now
                          select ac;

foreach (var appointment in appointments)
{
    if (appointment.Organizer == me)
    {
        // This takes 10 seconds
        comAppointment = appointmentsFromCom.FirstOrDefault(co => co.ID == appointment.ID);
        //....
    }
}
```

On first look the code looks OK. But when this piece is run the time it takes to iterate over the `appointmentsFromCom` is 10 seconds!

Due to deferred execution for each appointment where I am the organizer it will take 10 second. With only 6 appointments it will take up to 60 seconds to process, and it could be much more. The performance would not be acceptable.

Let's introduce `ToList` to alleviate the performance penalty caused by deferred execution:

```csharp
var appointments = from a in dataStore
                   where a.StartDate < DateTime.Now
                   select a;

var appointmentsFromCom = from ac in comObject
                          where ac.StartDate < DateTime.Now
                          select ac;
// This takes 10 seconds
appointmentsFromCom = appointmentsFromCom .ToList();

foreach (var appointment in appointments)
{
    if (appointment.Organizer == me)
    {
        comAppointment = appointmentsFromCom.FirstOrDefault(co => co.ID == appointment.ID);
        //....
    }
}
```

What happens now is that the query gets execute immediately when the `ToList`-methods is called. So the call to COM is done only once and always takes about 10 seconds. The `FirstOrDefault` is executed on the in-memory list and has no performance impact.

This sounds as the solution, but what if the appointments do not contain any items where I'm the organizer? It will always take 10 seconds to load, but it won't do anything with the data. With deferred execution enabled it would take no time as the query was never executed. The performance impact is much smaller than in the first example, but still significant for code that does not produce anything.

As a solution for this problem I created the Cache-method. This methods combines deferred execution and the static list to make sure the best performance is achieved.

```csharp
var appointments = from a in dataStore
                   where a.StartDate < DateTime.Now
                   select a;

var appointmentsFromCom = from ac in comObject
                          where ac.StartDate < DateTime.Now
                          select ac;
appointmentsFromCom = appointmentsFromCom .Cache();

foreach (var appointment in appointments)
{
    if (appointment.Organizer == me)
    {
        // This takes 10 seconds the first time it is used.
        comAppointment = appointmentsFromCom.FirstOrDefault(co => co.ID == appointment.ID);
        //....
    }
}
```

What does the Cache function do? It deferred executed the `ToList` method! It again returns a wrapper. When you iterate (in this case use `FirstOrDefault`) it executes a `ToList` and stores the result in the wrapper. Every subsequent time the wrapper object is used it just return the List it created and holds in memory.

This way we only have the 10 seconds hit once, but only if we're sure that the data is actually used. The best of both worlds.

Finally the code inside the Cache-method that makes the magic happen:

```csharp
public static class EnumerableExtensions
{
    /// <summary>
    /// Caches the specified source but used deferred execution.
    /// The first time the source is enumerated a cache of the items is created.
    /// Subsequent calls to enumerate the source will use the cache.
    /// </summary>
    /// <typeparam name="TSource">The type of the source.</typeparam>
    /// <param name="source">The source.</param>
    /// <returns></returns>
    public static IEnumerable<TSource> Cache<TSource>(this IEnumerable<TSource> source)
    {
        return new CacheIterator<TSource>(source);
    }

    /// <summary>
    /// 
    /// </summary>
    /// <typeparam name="TSource">The type of the source.</typeparam>
    private class CacheIterator<TSource> : IEnumerable<TSource>
    {
        private IEnumerable<TSource> _source;
        private int _initialThreadId;
        private List<TSource> _cache;

        /// <summary>
        /// Initializes a new instance of the <see cref="CacheIterator&lt;TSource&gt;"/> class.
        /// </summary>
        /// <param name="source">The source.</param>
        [DebuggerHidden]
        public CacheIterator(IEnumerable<TSource> source)
        {
            _source = source;
            _initialThreadId = Thread.CurrentThread.ManagedThreadId;
        }

        #region IEnumerable<T> Members

        /// <summary>
        /// Gets the enumerator.
        /// </summary>
        /// <returns></returns>
        [DebuggerHidden]
        public IEnumerator<TSource> GetEnumerator()
        {
            if (Thread.CurrentThread.ManagedThreadId == _initialThreadId)
            {
                if (_cache == null)
                    _cache = _source.ToList();

                return _cache.GetEnumerator();
            }
            else
            {
                var enumerator = new CacheIterator<TSource>(_source);
                return enumerator.GetEnumerator();
            }
        }

        #endregion

        #region IEnumerable Members

        /// <summary>
        /// Returns an enumerator that iterates through a collection.
        /// </summary>
        /// <returns>
        /// An <see cref="T:System.Collections.IEnumerator"/> object that can be used to iterate through the collection.
        /// </returns>
        [DebuggerHidden]
        System.Collections.IEnumerator System.Collections.IEnumerable.GetEnumerator()
        {
            return GetEnumerator();
        }

        #endregion
    }
}
```