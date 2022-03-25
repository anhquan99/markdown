# Culture sensitive
- To start, I would refer you to [.NET Globalization & Localization](https://docs.microsoft.com/en-us/dotnet/standard/globalization-localization/) for more information.

- There are certain values/entities that get treated differently in different countries and regions. Such entities include currency symbol, digit separators, dates and more. When displaying this data to your user, you have to format it in a way that makes sense to them. If for instance you display money in a loan amortization application as follows:

```csharp
Console.WritLine("$" + amount.ToString());
```

this will only work well in countries that use dollars. And there's no other formatting. To make this culture sensitive, you will have to do it this way...

```csharp
Console.WriteLine(amount.ToString("C"));
```

- By doing it that way, .NET will use the correct currency symbol, digit separator and decimal point for the culture the application is run in.

- There are cases in which you will need to show such data in a specific culture. Doing it this way will change the way it is displayed, you can however provide the culture to use in the following way...

```csharp
static void Main(string[] args)
{
    decimal foo = 23434534.53M;

    Dump(foo);

    var culture = CultureInfo.CreateSpecificCulture("en");
    CultureInfo.CurrentCulture = culture;
    Dump(foo);

    culture = CultureInfo.CreateSpecificCulture("es");
    CultureInfo.CurrentCulture = culture;
    Dump(foo);

    culture = CultureInfo.CreateSpecificCulture("en-ZA"); // South Africa
    CultureInfo.CurrentCulture = culture;
    Dump(foo);
}

static void Dump(decimal value)
{
    Console.WriteLine(value.ToString("C"));
    Console.WriteLine(DateTime.Now.ToLongDateString());
}
```

- When you change the culture at run time, subsequent calls to display the value as money will change the currency symbol, the location of said currency symbol in some cases, the digit separators and the decimal point.

- This also affects how dates are displayed as you can see.

- You can also use this to change the text displayed in your UI. If you have an app that supports multiple languages, for instance US English, British English, Spanish and French you are not required to know these languages beforehand. Also, you can no longer hard code you string literals like "First name:". Some simple things may differ like the spelling of color (UK English color). Such things might be inconsequential to someone who doesn't speak English but are taken seriously in the respective countries. If your app is showing children the correct spellings for instance, you want it to get it right for the place it is in.

- In this case you can set a UI culture and use resource (resx) files to place your strings in. At runtime you load your strings from the resource files and the .NET runtime can load the correct resx file based on the culture it detects the PC is set to. If there is no match, it will fall back to your default culture.

- In my test app I created 2 resource files, one called Strings.resx and the other Strings.es.resx. The first will be the default language, and the other is for Spanish. When greeting a user, I won't have to hard code the greeting but can use it as follows

```csharp
static void Main(string[] args)
{
    Console.WriteLine(ConsoleApp1.Strings.HelloString);

    CultureInfo.CurrentUICulture = CultureInfo.CreateSpecificCulture("es");
    Console.WriteLine(ConsoleApp1.Strings.HelloString);
}
```

resulting in the following

![[Pasted image 20220325185618.png]]

- Together these techniques can help you create an application that is sensitive to the language and culture of the user. If you need to override that, you can also do that knowingly by specifying which culture to use when presenting your data. Hard coding the entities that vary is not culture sensitive and will display the same regardless of the locale of the machine it is run on.
# Based on article: [https://stackoverflow.com/questions/51684170/culture-insensitive-vs-culture-sensitive]