# Prep 7: LLM APIs and MCP

1. The reply I got to "How many are in it?" + history is below:

The Twix Fun Size bag we have in stock is the **10.83-ounce bag**, which typically contains around **18 to 20 fun-size bars** (two-to-a-pack miniature Twix bars). \n\nIt's the perfect size for handing out to trick-or-treaters... assuming you don't eat half of them yourself while watching horror movies! \n\nWould you like me to add a bag to your cart?

Neither request stated the number of bars in a bag, but the model returned a quantity "18 to 20" without the context providing that info.

<hr>

2. The JSON model returned (as a string) was:

```
{\n  \"name\": \"Kit Kat Snack Size\",\n  \"price\": 5.99,\n  \"inStock\": true\n}
```

or as JSON:

```
{
    "name": "Kit Kat Snack Size",
    "price": 5.99,
    "inStock": true
}
```

<hr>

3. My script's output for the KitKat run was:

```
Request 1 → call searchCatalog({"query":"Kit Kat"}), result {"items":[{"item":"Kit Kat","size":"fun size, bag of 20","price":5.49}]}
Request 2 → call addToCart({"item":"Kit Kat","quantity":2}), result {"error":"OUT_OF_STOCK","item":"Kit Kat","inStock":0}
Request 3 → text: I'm sorry, but Kit Kats are currently out of stock. Would you like to try something else?
Cart: {}
```

<hr>

4. The DeepWiki tools were `ask_wiki_question`, `read_wiki_contents`, and `read_wiki_structure`. None of the declarations included an `annotations` field.