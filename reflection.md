# Advanced Agentic Patterns Lab - Reflection Questions

## Problem 1: Streaming Stock Agent

**1. How did the LLM know to call your new tool?**
The LLM knew to call the new tool because we registered its schema (including the `name`, `description`, and `parameters`) in the `STOCK_TOOLS` list, which is passed to the LLM as part of its context context. When the user's natural language request (e.g., "compare Apple and Microsoft") aligns with the semantic meaning of the tool's `description`, the LLM recognizes the intent and determines that invoking this specific tool is the appropriate action to fulfill the request.

**2. What happens if the tool schema description is unclear?**
If the description is ambiguous or unclear, the LLM may experience routing errors or "hallucinate". It might fail to invoke the tool when it is actually needed, mistakenly call the wrong tool, or attempt to answer the user's query purely from its pre-trained knowledge base rather than utilizing the real-time data provided by the tool. A precise description is critical for accurate tool selection.

**3. How does the agent decide between compare_stocks and get_stock_price?**
The agent relies heavily on the `description` fields provided in the schema of each tool. While `get_stock_price` is described as a tool to fetch the current price for a *single* stock ticker, `compare_stocks` is explicitly described as a tool to "Compare two stocks side-by-side." The LLM uses natural language processing to understand whether the user is asking about an individual entity or requesting a comparison between multiple entities, routing the request to the best matching description.

**4. How would you add validation for parameter values?**
Parameter validation can be implemented at two levels:
1. **Schema Level:** Enforce constraints directly within the JSON Schema (e.g., defining string length limitations, or using regular expressions to ensure the symbol only contains valid alphabetical characters).
2. **Execution Level (Code):** Add programmatic checks inside the `_compare_stocks()` Python function (e.g., using a `try-except` block). If the API fails or returns an invalid response for a given symbol, the function can safely catch the error and return a structured JSON error message (like `{"error": "Invalid ticker symbol"}`) so the LLM knows the tool execution failed and can inform the user gracefully.

**5. What if the user asks to compare 3 stocks instead of 2?**
Since the `compare_stocks` tool schema mandates exactly two arguments (`symbol1` and `symbol2`), the LLM cannot pass three symbols in a single call. In this scenario, the LLM might exhibit unexpected behavior: it might decline the prompt, attempt to call the tool multiple times sequentially (e.g., comparing A and B, then B and C), or hallucinate an answer. To officially support comparing three or more stocks in one go, the schema and function would need to be refactored to accept an array of strings (e.g., `symbols: List[str]`) rather than strictly two individual string parameters.
