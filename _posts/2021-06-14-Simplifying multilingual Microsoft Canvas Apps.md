---
title: Simplifying multilingual Microsoft Canvas Apps
tags: ["Power Platform",  "Power Apps", "Multilingual", "Languages", "Dataverse", "Internationalization", "i18n", "Canvas Apps" ]
image: /assets/2021-06-14/header.png
---
The [Microsoft Power Platform](https://www.prodwaregroup.com/our-solutions/microsoft-power-platform/) allows us to easily build Apps for our customers and our colleagues. In multi-national organizations this means that your app probably needs to support multiple languages. So how do we do that in Canvas Apps?

This is not the first article to tackle this topic. There are different solutions that are described on the internet. But I have added a bit of software engineering on top of that to create, in my opinion, a better solution. Not so much easier, but it puts the difficult parts in one place, to make the other parts easier to use and understand.
<!--more-->
## What’s wrong with existing multilingual Canvas App solutions?

This article builds on the existing examples. As input, the following articles and blogs are used:
- Microsoft Docs for Power Apps: [Language function in Power Apps](https://docs.microsoft.com/en-us/powerapps/maker/canvas-apps/functions/function-language)
- Microsoft’s first blog on the subject from 2017: [Building multilingual apps in PowerApps](https://powerapps.microsoft.com/en-us/blog/building-multilingual-apps-in-powerapps/)
- YouTube video using Automatic translations from 2019: [Support Multiple Languages in your PowerApps application](https://www.youtube.com/watch?v=aEyvrDyo6zc)
- Blog that applies some engineering to the problem: [How-To: Creating Multi-Language Canvas Apps](https://lennartwoermer.de/2020/05/how-to-creating-multi-language-canvas-apps/)

The core issue I have with these solutions is that they are all based on using ‘complex’ expressions. Take the following example, where we get the translation for a single label:

![Translation using Lookup](/assets/2021-06-14/lookup1.png)

```Power Fx
LookUp('Resource Strings', Key = "AppTitle" And Language.'ISO Code' = Language(), Value)
```

After this article you will be able to write just this:

![Translation using this method](/assets/2021-06-14/varstrings1.png)

```Power Fx
varStrings.AppTitle
```
## Power Apps translations in practice
This is not a very complex example. The original expression works fine for these simple texts. In practice, most [Power Apps](https://www.prodwaregroup.com/our-solutions/microsoft-power-platform/microsoft-power-apps/) need more than simple texts to display their data. Imagine the following scenario. The App has a text that shows if a user has a reservation: 

> On April 21 Joe has a reservation.

Let’s build that text using an expression:

```Power Fx
LookUp(
    'Resource Strings',
    Key = "On" And Language.'ISO Code' = Language(),
    Value
) & " " & Text(
    varDate,
    "mmmm d"
) & " " & varUserName & " " & LookUp(
    'Resource Strings',
    Key = "Reservation" And Language.'ISO Code' = Language(),
    Value
)
```

That already starts to look more complicated. You need to look a few times to see what is going on there. It’s hard to see the logic between the lookups. Also, we are making a few assumptions in this expression. These assumptions will become a problem if you want to translate this app correctly. 
- The structure of the text is the same in all languages. This is not true. For example, in Dutch you would write “Joe heeft op 21 april een reservering”. As you can see the structure of the sentence is different, with the Joe mentioned before the date.
- The Dutch example also shows the second assumption: that the date format is the same in all languages. This is also not true. As you can see in Dutch the date is before the month. This also applies for number formatting

So, let’s try to fix this by using a ‘template’; a text that contains placeholders for specific values. The template can be defined as:
- For English “On {0} {1} has a reservation”
- For Dutch “{1} heeft op {0} een reservering”
- {0} is the formatted date; {1} will be the name of the user

Using the `Substitute()`-function in the expression we can then replace the `{0}` and `{1}` with the correct values. We can also define an extra `DateFormat` that holds how the date needs to be formatted in a specific language. Combine that and we get the following Expression:

```Power Fx
Substitute(
    Substitute(
        LookUp(
            'Resource Strings',
            Key = "HasReservation" And Language.'ISO Code' = Language(),
            Value
        ),
        "{0}",
        Text(
            varDate,
            LookUp(
                'Resource Strings',
                Key = "DateFormat" And Language.'ISO Code' = Language(),
                Value
            )
        )
    ),
    "{1}",
     varUserName
)
```

There is a lot to unpack here, so the expression is no longer easy to understand. It is hard to read and hard to maintain. A citizen developer could be dissuaded by this. This is a perfect example of why a good multilingual solution is needed.
## Explaining the multilingual app solution - how var strings helps
In this solution we will be separating the translation logic and the business logic. The goal is to have the following expression, based on the previous example. It focusses just on the business logic. In this case, the substitution of the correct values.

```Power Fx
Substitute(
    Substitute(
        varStrings.HasReservation,
        "{0}",
        Text(
            varDate,
            varStrings.DateFormat
        )
    ),
    "{1}",
    varUserName
)
```

All the parts pertaining to getting the correct translated texts are no longer in this expression but are tucked away in the magic of `varStrings`.

The underlying solution is to prepare a Record (in this case varStrings) which contains all the translations string. When creating this record all these `Lookup` queries can be done. So, there will be one place where all the Lookup code is done. This will be a big (and a bit ugly looking) piece of code. The big upside is that there is only one place where this code resides! Everywhere else you can use the varStrings-variable.

So how does this work? 

![Set varStrings](/assets/2021-06-14/setvarstrings1.png)

```Power Fx
Set(varStrings, {
        AppTitle: LookUp('Resource Strings', Key = "AppTitle" And Language.'ISO Code' = Language(), Value),
        HasReservation: LookUp('Resource Strings', Key = "HasReservation" And Language.'ISO Code' = Language(), Value),
        DateFormat: LookUp('Resource Strings', Key = "DateFormat" And Language.'ISO Code' = Language(), Value)
});
```

We use `Set()` to create a variable that contains a Record. You can create any record using the `{ }` syntax. In this case our record has three keys: AppTitle, HasReservation and DateFormat. These are now properties of `varStrings`. So everywhere in your app where you use `varStrings` it will have these properties. As you can see, all the lookups are now bundled into one place in the code too.
## Structuring your translations
Using Records is a very useful feature. Especially since you can nest records inside records! This makes it possible to add structure to your records. An example is to group your translations based on the screen it is used in. 
 
![Structure in varStrings](/assets/2021-06-14/setvarstrings2.png)

```Power Fx
Set(varStrings, {
    Global: {
        AppTitle: LookUp('Resource Strings', Key = "AppTitle" And Language.'ISO Code' = Language(), Value),
        DateFormat: LookUp('Resource Strings', Key = "DateFormat" And Language.'ISO Code' = Language(), Value)
    },
    'Simple Example': {
        HasReservation: LookUp('Resource Strings', Key = "HasReservation" And Language.'ISO Code' = Language(), Value)
    },
    About: {}
});
```

You can now access the strings based on the groups Global, Simple Example and About. For example:

![Translation using varStrings](/assets/2021-06-14/varstrings2.png)

By using records in Power Apps you can easily create structured data. These structures can then be easily used in the Power App. The App Studio will automatically recognize your data structure so that you can autocomplete your code.

![Autocomplete for Translations](/assets/2021-06-14/autocomplete.png)

If the logic of retrieving the Resource string ever changes there is one place to maintain. Maybe you start out using an Excel sheet to hold the Translations and later switch to a Dataverse or SharePoint. As long as the structure of your Records remains the same, the app will keep working without errors.
## Further thoughts on developing a multilingual Power App
In the examples above, things have been simplified for brevity or ease of explanation. When building an app based on this design you should take care of the following:
- The resource strings are now retrieved one-by-one. This can be rather slow for many resources. See if you can grab just all resources in one call and then build the records based on the retrieved collection.
- The example uses ISO-Code and the [`Language()`-function](https://docs.microsoft.com/en-us/powerapps/maker/canvas-apps/functions/function-language). `Language()` resolves the user’s region, not language, so en-US or nl-NL. You should take care of handling this, for example by only using the first two letters. Also think about defaults when an unknown language is encountered.
- You can easily change the `Language()`-function to use another variable. This enables you to let the end-user choose his own language. Just rebuild the `varString` record based on the newly selected language.
## Business functionality over code complexity in developing a Power App 
There are many things to take into account when creating a full featured multilingual app. Some of these things can be made easy by rearranging the logic in your app.

It is easy to use the Record type in Microsoft Power Apps to structure your data. By using this feature, you can create custom structures that fit your need. In this case we use it to structure our resources strings.

The structuring of the data happens in a single place. At that moment, the code might be complex in order to retrieve the correct string for the current resource key. But all this complexity is contained in a single place. 

Using resource string in the screens is now focused on functionality instead of the complexities of retrieving the correct resources. This makes it much easier to focus on the business functionality your app is delivering. And it makes it easier to support and maintain your app going forward.

If you are looking at developing a multilingual Power App for your organization, and have found my hints and tips useful but want more support, then please reach out to me at Prodware.