![image](https://github.com/ADefWebserver/MonacoRazor/blob/main/example.png)

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
- `OnCompletionsRequested`  - A method that is called when the editor requests completions.
- `Height` - The height of the editor.
- `Width` - The width of the editor.
- `Border` - The border of the editor.
- `Margin` - The margin of the editor.
- `MinHeight` - The minimum height of the editor.

**Methods**

- `GetCodeAsync()` - Gets the content of the editor.
- `UpdateCodeAsync(code)` - Updates the content of the editor.

## Basic Example

<details>
<summary>Code</summary>

```cs
@page "/"
@using SimpleBlazorMonaco
<h1>Hello, world!</h1>

Welcome to your new app.

<CodeEditor @ref="@MonacoCodeEditor"
            Language="csharp"
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
                    <CodeEditor 
                        Language="csharp" 
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

    private string Message = "";

    private void CloseMessagePopup()
    {
        Message = "";
    }
}
```

</details>
