# Refactoring Form Inputs in Blazor

A common scenario in forms with web applications is dealing with tedious repetition when creating input fields. Many individual inputs have a common pattern of surrounding it with a label, validation, and some styling. Developers continuously find themselves in copy-paste scenarios with only a few small alterations for each input. The line count can easily explode into hundreds of lines with larger forms. Most of this markup falls under the category of line noise or boiler plate that must be there.

Every form field generally follows the same pattern:

- Input Field
- Validation text when the input is invalid
- Label for the input
- Styling for the input in form of css classes

The following example shows two input fields in a Blazor form. The first is a text input for a name, while the other is a number input for a person's age. For the sake of simplicity, only the input fields are displayed. The css classes come from the Bulma library.

```csharp
<div class="field">
    <label for="name-text-input" class="label">Name</label>
    <div class="control">
        <InputText class="input" id="name-text-input" @bind-Value="InputModel.Name"></InputText>
    </div>
    <p class="help is-danger">
        <ValidationMessage For="() => InputModel.Name"></ValidationMessage>
    </p>
</div>

<div class="field">
    <label for="age-number-input" class="label">Age</label>
    <div class="control">
        <InputNumber class="input" id="age-number-input" @bind-Value="InputModel.Age"></InputNumber>
    </div>
    <p class="help is-danger">
        <ValidationMessage For="() => InputModel.Age"></ValidationMessage>
    </p>
</div>
```

Although nothing is technically wrong with this markup, it is rather repetitive. As engineers, we should acknowledge the time-sinking and sanity-draining exercise of repeating, etc., etc.. Forms can grow with ever-more fields, drastically increasing the line count in a component. As developers, the infrastructure around the input is secondary. We should only have to declare a single element with all the secondary elements being added automatically.

If we sift through the html and razor markup, we can see the real differences:

- Type of input
- Model being passed to the input and validation
- Label name and content

From this, we can create a common interface that will accept these parameters and render the same result. Our desired solution should look like so:

```csharp
<TextInput @bind-Value="InputModel.Name" Label="Name" Validation="() => InputModel.Name" />
    
<NumberInput @bind-Value="InputModel.Age" Label="Age" Validation="() => InputModel.Age" />
```

This ideal implementation is a single line solution that will that contain all the markup and razor necessary to render a complete input field. We will cut the numbers of lines required for each input from around nine to one. This will simplify our application and line count, ultimately saving time.

Reviewing the current implementation for the name field:

```csharp
<div class="field">
    <label for="name-text-input" class="label">Name</label>
    <div class="control">
        <InputText class="input" id="name-text-input" @bind-Value="InputModel.Name"></InputText>
    </div>
    <p class="help is-danger">
        <ValidationMessage For="() => InputModel.Name"></ValidationMessage>
    </p>
</div>
```

What we need is two components for each field:

- An input component that is specific to the input type (text, number, email)
- A container around the component that has the styling, label, and validation

We begin with our container component. If we extract everything around the component and leave a RenderFragment for our input, we should get the following:

```csharp
@using System.Linq.Expressions
@typeparam TValue

<div class="field">
    <label for="@DisplayName" class="label">@DisplayName</label>
    <div class="control">
        @ChildContent
    </div>
    <p class="help is-danger">
        <ValidationMessage For="@ValidationFor"></ValidationMessage>
    </p>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string DisplayName { get; set; }

    [Parameter]
    public Expression<Func<TValue>> ValidationFor { get; set; }
}
```

This is called our "InputContainer" component. All we have done here is replaced the specific input with a RenderFragment, allowing us to reuse the container for every specific input. Therefore, we have a parameter for our label and the validation expression, both of which will be used to validate the contents of the input.

With our InputContainer in place, we need to tackle our individual input components by creating one component per input type. While this may seem like a hassle at first, it will pay dividends later. Creating each input component is trickier than our container. Each input needs to inherits the generic "InputBase" class and implement the "TryParseValueFromString" method. This method is responsible for taking the value from the input and parsing it to the specific type for the model we are trying to populate.

Our TextInput component:

```csharp
@using System.Linq.Expressions
@inherits InputBase<string>

<InputContainer ValidationFor="Validation" DisplayName="@Label">
    <input class="input @CssClass" id="@Label" type="text" @bind="@CurrentValue" />
</InputContainer>


@code {

    [Parameter]
    public string Label { get; set; }

    [Parameter]
    public Expression<Func<string>> Validation { get; set; }


    protected override bool TryParseValueFromString(string value, out string result, out string validationErrorMessage)
    {
        result = value;
        validationErrorMessage = null;
        return true;
    }

}
```

A quick review of what this component is accomplishing:

1) Inherits from InputBase of type string because this is a text input
2) Declares an input of type text and passes the label as an id
3) Adds the "CssClass" property, which is part of InputBase class, allowing Blazor to add the "valid" and "invalid" classes during validation
4) Adds two-way binding to the "CurrentValue" property inherited from the InputBase class
5) Passes the input to the "InputContainer" as a child, the label name, and validation expression
6) Implements the TryParseValueFromString method

For reference, our NumberInput becomes:

```csharp
@using System.Linq.Expressions
@inherits InputBase<int>

<InputContainer DisplayName="@Label" ValidationFor="@Validation">
    <input class="input" id="@Label" type="number" @bind="@CurrentValue" />
</InputContainer>

@code {
    [Parameter]
    public string Label { get; set; }

    [Parameter]
    public Expression<Func<int>> Validation { get; set; }

    protected override bool TryParseValueFromString(string value, out int result, out string validationErrorMessage)
    {
        if (int.TryParse(value, out var parsed))
        {
            result = parsed;
            validationErrorMessage = null;
            return true;
        }

        throw new InvalidOperationException("Can't parse to int.");
    }
}

```

The main difference is our TryParseValueFromString is forced to do extra logic when dealing with non-numeric strings.

But with our new compact components, our form content becomes:

```csharp
<TextInput @bind-Value="InputModel.Name" Label="Name" Validation="() => InputModel.Name" />
    
<NumberInput @bind-Value="InputModel.Age" Label="Age" Validation="() => InputModel.Age" />
```

The amount of markup you will be able to remove in your application ultimately comes down to how many forms you have and the size of those forms. For applications with minimal form usage, this technique may not make a huge impact. Yet, for applications with many forms the opportunity to reduce inputs by up to ninety percent cannot be overstated or ignored.
