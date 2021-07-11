# Refactoring form inputs in Blazor

A common scenario in forms with any web application is dealing with a lot of repetition when creating input fields. Many individual inputs have a common pattern of taking an input tag and surrounding it with a label, validation, and some styling. Developers continuously find themselves in copy-paste scenarios with code where only a few small details change for each input. With larger forms, the line count can easily explode into the hundreds of lines. Most of this markup falls under the category of line noise or boiler plate that just has to be there.

Every form field generally follows the same pattern of:

- The input field itself
- Validation text when the input is invalid
- A label for the input
- Styling for the input in form of css classes

The following example shows two input fields for a form in Blazor. One is a text input for a name, the other is a number input for a person's age. For the sake of simplicity I am only showing the input fields. The css classes come from the Bulma library.

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

While there is nothing wrong with this markup, it is repetitive. As engineers, we should see the tedious nature of having to write the same code over and over. Forms can grow with many more fields will drastically increase the line count in a component. As developers, the infrastructure around the input is secondary. We should only have to declare a single element with all of the secondary elements being added automatically.

If we sift through the html and razor markup, we can see the only real differences are:

- The type of the input
- The model being passed to the input and validation
- The label name and content

From this we can create a common interface that will accept these parameters and render the same result. Our desired solution should look like:

```csharp
<TextInput @bind-Value="InputModel.Name" Label="Name" Validation="() => InputModel.Name" />
    
<NumberInput @bind-Value="InputModel.Age" Label="Age" Validation="() => InputModel.Age" />
```

This ideal implementation is a single line solution that will that contain all the markup and razor necessary to render a complete input field. We will have cut the numbers of line needs for each input from around nine to one. This will simplify our application and line count.

Lets review the current implementation again for the name field again:

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

Lets start with our container component first. If we extract everything around the component and leave a RenderFragment for our input we should get the following:

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

I call this our "InputContainer" component. All we have done here is replaced the specific input with a RenderFragment. This will allow us to reuse the container for every specific input. We have a parameter for our label and the validation expression which will be used to validate the contents of the input.

With our InputContainer in place, we need to tackle our individual input components. We will need to create one component per input type. While this may seem like a hassle right now, it will pay dividends later. Creating each input component is trickier than our container. Each input needs to inherits the generic "InputBase" class and implemented the "TryParseValueFromString" method. This method is responsible for taking the value from the input and parsing it to the specific type for the model we are trying to populate.

Our TextInput component should look like:

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

Lets go over what this component is doing:

1) Inherits from InputBase of type string because this is a text input
2) Passes an input to the "InputContainer" component, alongside the label name and validation expression which are parameters for the component
3) Declares an input of type text, and passes the label as an id
4) Adds the "CssClass" property, which is part of InputBase class. This allows Blazor to add the "valid" and "invalid" classes during validation
5) Adds two-way binding to the "CurrentValue" property inherited from the InputBase class
6) Implements the TryParseValueFromString method

For reference our NumberInput can now become:

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

The main difference is our TryParseValueFromString needs to do some extra logic for dealing with non-numeric strings.

With our new compact components our form content can finally become:

```csharp
<TextInput @bind-Value="InputModel.Name" Label="Name" Validation="() => InputModel.Name" />
    
<NumberInput @bind-Value="InputModel.Age" Label="Age" Validation="() => InputModel.Age" />
```

The amount of markup you will be able to remove in your application ultimately comes down to how many forms and the size of those forms. For applications with minimal form usage, this technique may not make a large difference. For applications with a large number of forms, the opportunity to reduce inputs by up to ninety percent can not be overlooked.
