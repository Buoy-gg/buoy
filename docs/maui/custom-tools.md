---
title: Custom tools
seoTitle: "Build custom .NET MAUI dev tools with Buoy"
id: maui-custom-tools
description: "Add your own tool to the Buoy menu in a MAUI app. Give it a screen, and let Buoy Desktop and AI agents read it and run its actions."
---

You can add your own tool to the Buoy menu.
It gets a spot on the dial, like the built-in tools.
Finish the [Quick start](./quick-start) first.

## Add a tool

Add the tool inside `UseBuoy`.
Give it an id, a name, a short line and an icon.
Then give it a view to show.

```text
#if DEBUG
using Buoy.Core;
using Buoy.Maui;
#endif

builder.UseBuoy(options =>
{
    options.Tools.Add(new(
        new ToolDefinition("cart", "Cart", "See the cart.", "storage"),
        context => new CartToolView(context)));
});
```

The icon name picks one of Buoy's tool icons.
Here, `"storage"` uses the Storage icon.
Pass `CreateIcon` to draw your own icon instead.

Keep the id short, with no spaces and no `:`.
Buoy saves your tool's place on the dial under this id.

## Build the view

The view is a normal MAUI `View`.
Buoy hands it a `BuoyToolContext`.

```text
sealed class CartToolView : ContentView, IDisposable
{
    readonly BuoyToolContext _context;
    readonly VerticalStackLayout _body = new() { Spacing = 12, Padding = 16 };

    public CartToolView(BuoyToolContext context)
    {
        _context = context;
        Content = new ScrollView { Content = _body };
        context.SearchChanged += OnSearch;
        Render();
    }

    void OnSearch(string _) => Render();

    void Render()
    {
        _body.Clear();
        _body.Add(BuoyTheme.Label("3 items", 14, BuoyTheme.Accent, true));
        _body.Add(BuoyTheme.Button("Clear cart", "cart-clear", async () =>
        {
            await ClearCartAsync();
            _context.ShowToast?.Invoke("Cart cleared");
        }));
    }

    public void Dispose() => _context.SearchChanged -= OnSearch;
}
```

The context gives you these:

- `Services` is your app's service provider.
- `SearchText` and `SearchChanged` follow the search box.
  Set `Searchable: true` on the `ToolDefinition` to show it.
- `ShowToast` shows a short note in the tool window.
- `Close`, `Minimize` and `ToggleMode` move the window.
- `CancellationToken` ends when the window closes.

Buoy disposes your view when its window closes.
So drop event handlers in `Dispose`, like above.

`BuoyTheme` has the labels, buttons and cards the built-in tools use.
Use it so your tool looks like the rest of Buoy.

## Let Desktop and AI use it

Give the tool an `IToolSyncAdapter`.
Then Buoy Desktop can show its data.
AI agents can read it and run its actions over [MCP](../mcp).
They reach it as `custom:<id>`, like `custom:cart`.
This works even when the tool's window is closed.

```text
options.Tools.Add(new(
    new ToolDefinition("cart", "Cart", "See the cart.", "storage"),
    context => new CartToolView(context),
    new CartSyncAdapter(cart)));
```

```text
sealed class CartSyncAdapter(Cart cart) : IToolSyncAdapter
{
    public int Version => 1;

    public object GetSnapshot() => new { items = cart.Items, count = cart.Items.Count };

    public IDisposable Subscribe(Action onChange) => cart.Subscribe(onChange);

    public IReadOnlyDictionary<string, Func<JsonElement, CancellationToken, Task<object?>>> Actions =>
        new Dictionary<string, Func<JsonElement, CancellationToken, Task<object?>>>
        {
            ["clear"] = async (parameters, token) =>
            {
                await cart.ClearAsync(token);
                return GetSnapshot();
            },
        };
}
```

`GetSnapshot` is what Desktop and agents read.
Only put data in it that they may see.
Call the `Subscribe` callback after each change.
Then Buoy sends a fresh snapshot.

Each action gets its input as JSON.
Check that input first, and throw if it is wrong.
The error text goes back to the caller.

## Ship a tool in a package

Do you want to share a tool across apps?
Then build it as an `IBuoyPlugin`.

```text
sealed class CartPlugin(Cart cart) : IBuoyPlugin
{
    public IEnumerable<BuoyTool> CreateTools(IServiceProvider services) =>
    [
        new(new ToolDefinition("cart", "Cart", "See the cart.", "storage"),
            context => new CartToolView(context),
            new CartSyncAdapter(cart)),
    ];

    public Task StartAsync(CancellationToken cancellationToken) => Task.CompletedTask;
    public Task StopAsync(CancellationToken cancellationToken) => Task.CompletedTask;
    public ValueTask DisposeAsync() => ValueTask.CompletedTask;
}
```

Add it with `builder.Services.AddBuoyPlugin<CartPlugin>()`.
Buoy calls `StartAsync` once your account can use tools.
It calls `StopAsync` when that access ends.
Turn off any capture in `StopAsync`.

A tool you add in `options.Tools` wins over a plugin tool with the same id.

## Keep it out of release builds

Wrap your tool code in the same Debug guards as Buoy.
See [Release builds](./release-builds).
