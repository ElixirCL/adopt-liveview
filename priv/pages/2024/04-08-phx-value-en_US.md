%{
title: "phx-value",
author: "Lubien",
tags: ~w(getting-started),
section: "Events",
description: "How to pass data with events"
}

---

In previous classes we only saw how to trigger events. In the conditional rendering class with `cond` we saw the following code:

```elixir
Mix.install([
  {:liveview_playground, "~> 0.1.1"}
])

defmodule PageLive do
  use LiveviewPlaygroundWeb, :live_view

  def mount(_params, _session, socket) do
    socket = assign(socket, temperature_celsius: 30)
    {:ok, socket}
  end

  def render(assigns) do
    ~H"""
    <div>
      Current temperature: <%= @temperature_celsius %>C
    </div>
    <div>
      <%= cond do %>
        <% @temperature_celsius > 40 -> %>
          <p>🔥 Impossible to live 🔥</p>
        <% @temperature_celsius > 30 -> %>
          <p>Its hot</p>
        <% @temperature_celsius > 20 -> %>
          <p>Kinda cool</p>
        <% @temperature_celsius > 10 -> %>
          <p>Chill</p>
        <% @temperature_celsius > 0 -> %>
          <p>Chill</p>
        <% true -> %>
          <p>❄️⛄️</p>
      <% end %>
    </div>

    <input type="button" value="Increase" phx-click="increase" />
    <input type="button" value="Decrease" phx-click="decrease" />
    """
  end

  def handle_event("increase", _params, socket) do
    socket = assign(socket, temperature_celsius: socket.assigns.temperature_celsius + 10)
    {:noreply, socket}
  end
  def handle_event("decrease", _params, socket) do
    socket = assign(socket, temperature_celsius: socket.assigns.temperature_celsius - 10)
    {:noreply, socket}
  end
end

LiveviewPlayground.start()
```

Our code always increased or decreased the temperature by 10 degrees. What if we wanted options to increase by different amounts? Would we have to have an event for each case? Let's get to know the `phx-value` binding. Create and run the `phx_value.exs` file:

```elixir
Mix.install([
  {:liveview_playground, "~> 0.1.1"}
])

defmodule PageLive do
  use LiveviewPlaygroundWeb, :live_view

  def mount(_params, _session, socket) do
    socket = assign(socket, temperature_celsius: 30)
    {:ok, socket}
  end

  def render(assigns) do
    ~H"""
    <div>
      Current temperature: <%= @temperature_celsius %>C
    </div>
    <div>
      <%= cond do %>
        <% @temperature_celsius > 40 -> %>
          <p>🔥 Impossible to live 🔥</p>
        <% @temperature_celsius > 30 -> %>
          <p>Its hot</p>
        <% @temperature_celsius > 20 -> %>
          <p>Kinda cool</p>
        <% @temperature_celsius > 10 -> %>
          <p>Chill</p>
        <% @temperature_celsius > 0 -> %>
          <p>Chill</p>
        <% true -> %>
          <p>❄️⛄️</p>
      <% end %>
    </div>

    <input type="button" value="+5" phx-click="add" phx-value-amount={+5} />
    <input type="button" value="+10" phx-click="add" phx-value-amount={+10} />
    <input type="button" value="-5" phx-click="add" phx-value-amount={-5} />
    <input type="button" value="-10" phx-click="add" phx-value-amount={-10} />
    """
  end

  def handle_event("add", %{"amount" => amount}, socket) do
    amount = String.to_integer(amount)
    socket = assign(socket, temperature_celsius: socket.assigns.temperature_celsius + amount)
    {:noreply, socket}
  end
end

LiveviewPlayground.start()
```

This time we replace the `"increase"` and `"decrease"` events with a generic event called `"add"` that receives a `phx-value-amount` which is a number. Equivalently, your `handle_event/3` receives `%{"amount" => "+10"}` as a second parameter depending on the button clicked. It is important to note that parameters coming from HTML always come in string format (as HTML attributes are strings) so we must convert the number before doing the sum.

## In short!

- The `phx-value-*` binding can help generalize an event so that it is reusable.
- The data you receive in `handle_event/3` is equivalent to the HEEx binding name: `phx-value-test` generates `%{"test" => value}`.
- Values coming from a `phx-value-*` binding are mandatory strings.