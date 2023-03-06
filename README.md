<p align="center">
  <img width="400" height="62" src="meilisearch-ex.png">
</p>

An **unofficial** [Meilisearch](https://www.meilisearch.com/) client based on [Tesla](https://github.com/elixir-tesla/tesla) HTTP client.

## Installation

:warning Not published to hex yet, I still need to do some real world tests.

If [available in Hex](https://hex.pm/docs/publish), the package can be installed
by adding `meilisearch_ex` to your list of dependencies in `mix.exs`:

```elixir
def deps do
  [
    {:meilisearch_ex, "~> 1.0.0"}
  ]
end
```

Documentation can be generated with [ExDoc](https://github.com/elixir-lang/ex_doc)
and published on [HexDocs](https://hexdocs.pm). Once published, the docs can
be found at <https://hexdocs.pm/meilisearch-ex>.

## Usage

You can create a client when you needs it.

```elixir
[endpoint: "https://search.mydomain.com", key: "replace_me"]
|> Meilisearch.Client.new()
|> Meilisearch.Health.get()

# %Meilisearch.Health{status: "available"}
```

But you can also start a client alongside your application to access it whenever you need it.

```elixir
Meilisearch.start_link(:main, [endpoint: "https://search.mydomain.com", key: "replace_me"])

:main
|> Meilisearch.client()
|> Meilisearch.Health.get()

# %Meilisearch.Health{status: "available"}
```

Within a Phoenix app you would do like this:

```elixir
defmodule MyApp.Application do
  # ...

  @impl true
  def start(_type, _args) do
    children = [
      # ...
      {Meilisearch, name: :search_admin, endpoint: "https://search.mydomain.com", key: "key_admin"},
      {Meilisearch, name: :search_user, endpoint: "https://search.mydomain.com", key: "key_user"}
    ]

    # ...
  end

  # ...
end

defmodule MyApp.MyContext do
  def create_search_index() do
    :search_admin
    |> Meilisearch.client()
    |> Meilisearch.Index.create(%{uid: "items", primaryKey: "id"})
  end

  def add_documents_to_search_index(documents) do
    :search_admin
    |> Meilisearch.client()
    |> Meilisearch.Document.create_or_replace("items", documents)
  end

  def search_document(query) do
    :search_user
    |> Meilisearch.client()
    |> Meilisearch.Search.search("items", %{q: query})
  end
end
```

## Compatibility

For now, we only support version `1.0.x` of Meilisearch.

|  meilisearch  | meilisearch-ex |
| ------------- | ------------- |
|     1.0.x     |      1.0      |

