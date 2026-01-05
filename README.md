![image](https://raw.githubusercontent.com/ADefWebserver/MonacoRazor/main/example.png)
## Installing

You can install from NuGet using the following command:

`Install-Package SimpleBlazorMonaco`

Or via the Visual Studio package manger.

## Setup

You can add the following using statement to your main `_Imports.razor` to make referencing the component a bit easier.

```cs
@using MonacoRazor
@using SimpleBlazorMonaco
```

## Usage

Below is a list of all the options available on the Editor.

**Properties**

- `Language` - The language of the editor.
- `Code`  - The code to be displayed in the editor.
- `Height` - The height of the editor.
- `Width` - The width of the editor.
- `Border` - The border of the editor.
- `Margin` - The margin of the editor.
- `MinHeight` - The minimum height of the editor.

**Methods**

- `GetCodeAsync()` - Gets the content of the editor.
- `UpdateCodeAsync(code)` - Updates the content of the editor.
- `UpdateLanguage(language)` - Updates the language of the editor.
- `OnCompletionsRequested`  - A method that is called when the editor requests completions.

## Basic Example

<details>
<summary>Code</summary>

```cs
@page "/"
@using SimpleBlazorMonaco
<h1>Hello, world!</h1>

<h4>Welcome to your new app.</h4>

<div class="language-selector" style="margin-bottom:1em;">
    <label for="languageSelect">Select Language:</label>
    <select id="languageSelect"
            @onchange="@(async args => await DynamicUpdateLanguage(args.Value?.ToString()!))"
            value="@CurrentLanguage">
        <option value="csharp">C#</option>
        <option value="python">Python</option>
    </select>
</div>

<CodeEditor @ref="@MonacoCodeEditor"
            Language="@CurrentLanguage"
            Code="@CurrentScript"
            OnCompletionsRequested="GetCompletionsAsync"
            Height="300px"
            Width="90%"
            Border="2px solid gray"
            Margin="1em 0"
            MinHeight="300px" />
<br />
<button @onclick="GetCode">Get Code</button>&nbsp;
<button @onclick="UpdateCode">Update Code</button>

@if (!string.IsNullOrEmpty(Message))
{
    <div class="modal fade show" tabindex="-1" role="dialog"
         style="display: block;" aria-modal="true">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Current Script</h5>
                </div>
                <div class="modal-body">
                    <CodeEditor Language="@CurrentLanguage"
                                Height="90%"
                                Width="99%"
                                Border="0px"
                                Code="@Message" />
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary"
                            data-bs-dismiss="modal" @onclick="CloseMessagePopup">
                        Close
                    </button>
                </div>
            </div>
        </div>
    </div>
    <div class="modal-backdrop fade show"></div>
}

@code {
    CodeEditor MonacoCodeEditor;
    string CurrentScript = "";

    // default language
    private string CurrentLanguage { get; set; } = "csharp";

    string SampleScript = @"namespace Sample
    {
        public class MyClass
        {
        }
    }";

    protected override void OnInitialized()
    {
        CurrentScript = SampleScript;
    }

    private async Task DynamicUpdateLanguage(string language)
    {
        //set the field so the CodeEditor will pick up the new language
        CurrentLanguage = language switch
        {
            "python" => "python",
            _ => "csharp"
        };

        await UpdateLanguage(CurrentLanguage);
    }

    Task<Suggestion[]> GetCompletionsAsync(string currentValue, Position position)
    {
        return Task.FromResult(new[]
        {
            new Suggestion {Label = "From C#", InsertText = "From C#" },
            new Suggestion {Label = "OK", InsertText = "OK" },
        });
    }

    private async Task GetCode()
    {
        CurrentScript = await MonacoCodeEditor.GetCodeAsync();
        Message = CurrentScript;
    }

    private async Task UpdateCode()
    {
        await MonacoCodeEditor.UpdateCodeAsync(SampleScript);
    }

    private async Task UpdateLanguage(string language)
    {
        await MonacoCodeEditor.UpdateLanguageAsync(language);
    }

    private string Message = "";

    private void CloseMessagePopup()
    {
        Message = "";
    }
}
```

</details>
