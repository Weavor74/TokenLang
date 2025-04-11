# TokenLang: A Structured Command Language

## 1. Introduction

TokenLang is a specification for a structured command language designed for interacting with AI systems, data sources, and external services. Its primary goal is to provide an **efficient**, **explicit**, and **flexible** way to define sequences of operations, manage context, handle data, and control execution flow.

It uses a simple, token-based syntax (`;;token;; <content> [-flags]`) where short, predefined tokens represent specific actions. Commands can be chained together, allowing for the creation of complex workflows from simple building blocks. *(Syntax updated to ;;token;;)*

## 2. Key Features

* **Token-Based Syntax:** Simple and efficient commands like `;;gen;;`, `;;get;;`, `;;set;;`. *(Syntax updated)*
* **Command Chaining:** Pipe (`|`) operator to pass output from one command to the next.
* **Explicit Variable Handling:** Variables (`${var}`) must be explicitly set (`;;set;;`) with optional scope (`session`, `user`) and type hints. *(Syntax updated)*
* **Rich Control Flow:** Conditionals (`;;if;;`, `;;else;;`, `;;endif;;`), loops (`;;for;;`, `;;while;;`), and switch cases (`;;switch;;`, `;;case;;`). *(Syntax updated)*
* **Robust Error Handling:** Standardized error format and `;;try;;`/`;;catch;;`/`;;finally;;` blocks. *(Syntax updated)*
* **Data Query & Manipulation:** SQL-like commands for fetching (`;;get;;`, `;;from;;`, `;;where;;`, etc.) and modifying (`;;ins;;`, `;;upd;;`, `;;del;;`) data. *(Syntax updated)*
* **Transaction Support:** Group data modifications into atomic units (`;;begin_tx;;`, `;;commit_tx;;`, `;;rollback_tx;;`). *(Syntax updated)*
* **AI & Generation Tasks:** Commands for text generation (`;;gen;;`), translation (`;;trans;;`), summarization (`;;sum;;`), Q&A (`;;ans;;`), classification (`;;cls;;`), code generation (`;;code;;`), and multimedia output (`;;img;;`, `;;doc;;`, `;;vid;;`, `;;aud;;`). *(Syntax updated)*
* **External Integration:** Fetch content from URLs (`;;fetch_url;;`) and interact with external APIs (`;;api;;`). *(Syntax updated)*
* **Utilities:** User prompts (`;;ask;;`), state inspection (`;;insp;;`, `;;vars;;`), file I/O (`;;readf;;`, `;;writef;;`), built-in help (`;;help;;`). *(Syntax updated)*
* **Context Management:** Set runtime context for AI or system behavior (`;;ctx;;`). *(Syntax updated)*
* **Asynchronous Execution:** Support for running long tasks in the background (`-async` flag).
* **Extensibility:** Conceptual support for defining custom tokens (`;;def;;`). *(Syntax updated)*

## 3. Core Concepts

### 3.1. Basic Syntax *(Syntax Updated)*

Commands follow the pattern: `;;token;; <content> [-flag=value] [-boolean_flag]`

* `;;token;;`: The command keyword (e.g., `;;gen;;`, `;;get;;`).
* `<content>`: The main subject or data for the command.
* `[-flags]`: Optional named parameters modifying behavior.

### 3.2. Variables

Variables are placeholders like `${variable_name}`. They **must** be defined using `;;set;;` before use. *(Syntax updated)*

```tokenlang
;;set;; user_location = "Sonterra, Texas" -scope=user
;;set;; max_items = 10 -type=number

;;get;; products -where="location = '${user_location}'" -lim=${max_items}


(Example syntax updated)
3.3. Chaining
The pipe | sends the output of one command to the input of the next.
# Get active users, select name/email, format as table
;;from;; users | ;;where;; status='active' | ;;sel;; name, email | ;;get;; | ;;table;; -fmt=markdown


(Example syntax updated)
3.4. Control Flow
TokenLang supports standard control flow structures:
If/Else:
;;if;; ${count} > 0
  ;;gen;; Found ${count} items.
;;else;;
  ;;gen;; No items found.
;;endif;;

(Example syntax updated)
For Loop (List):
;;set;; items = '["apple", "banana"]' -type=list
;;for;; item IN ${items}
  ;;gen;; Processing: ${item}
;;endfor;;

(Example syntax updated)
While Loop:
;;set;; i = 0
;;while;; ${i} < 5
  ;;gen;; Count: ${i}
  ;;eval;; i = ${i} + 1
;;endwhile;;

(Example syntax updated)
3.5. Error Handling
Errors produce a standard JSON output. ;;try;;/;;catch;; blocks allow for graceful error management. (Syntax updated)
;;try;;
  ;;api;; [https://unreliable.example.com/data](https://unreliable.example.com/data) -var=result -err_var=api_err
  ;;if;; ${api_err} != ''
      ;;gen;; API Error: ${api_err.message}
  ;;else;;
      ;;gen;; API Success: ${result}
  ;;endif;;
;;catch;; -err=e
  ;;gen;; Critical failure during API call: ${e.message}
;;endtry;;


(Example syntax updated)
3.6. Transactions
Ensure atomicity for database operations.
;;begin_tx;;
  ;;ins;; orders -data='{...}'
  ;;upd;; inventory -where="sku='XYZ'" -set='{"count": ${current_count} - 1}'
  # If any command fails here, rollback occurs (implementation dependent)
;;commit_tx;;
# Or use ;;rollback_tx;; explicitly on error


(Example syntax updated)
4. Command Overview (Categories) (Syntax Updated)
AI/Generation: ;;gen;;, ;;trans;;, ;;sum;;, ;;ans;;, ;;cls;;, ;;code;;
Data Query: ;;get;;, ;;sel;;, ;;from;;, ;;where;;, ;;sort;;, ;;lim;;, ;;skip;;
Data Manipulation: ;;ins;;, ;;upd;;, ;;del;;
Transactions: ;;begin_tx;;, ;;commit_tx;;, ;;rollback_tx;;
Variables & Context: ;;set;;, ;;eval;;, ;;ctx;;
Control Flow: ;;if;;, ;;else;;, ;;endif;;, ;;for;;, ;;while;;, ;;break;;, ;;continue;;, ;;endfor;;, ;;endwhile;;, ;;switch;;, ;;case;;, ;;default;;, ;;endswitch;;
Error Handling: ;;try;;, ;;catch;;, ;;finally;;, ;;endtry;;
Output Formatting: ;;img;;, ;;doc;;, ;;vid;;, ;;aud;;, ;;json;;, ;;table;;
External Integration: ;;fetch_url;;, ;;api;;
Utilities & Meta: ;;ask;;, ;;insp;;, ;;vars;;, ;;readf;;, ;;writef;;, ;;def;;, ;;help;;
(Refer to the full TokenLang Specification v3.0 for detailed syntax and parameters of each command.)
5. Example Script (Syntax Updated)
# Set user variable and fetch config from a remote URL
;;set;; user_id = 123 -scope=user
;;fetch_url;; [https://config.example.com/settings.json](https://config.example.com/settings.json) -var=config_json -err_var=fetch_err

# Use try/catch for error handling
;;try;;
  # Check if fetch failed
  ;;if;; ${fetch_err} != ''
    ;;gen;; Error fetching config: ${fetch_err.message}
    # Potentially exit or rollback if in transaction
  ;;else;;
    # Parse config (assuming JSON content, implementation detail)
    # ;;set;; api_key = ${config_json.api_key} # Requires JSON parsing support

    # Fetch user data using API call
    ;;api;; [https://api.example.com/users/$](https://api.example.com/users/$){user_id} -var=user_data -err_var=api_err

    # Generate a personalized greeting
    ;;if;; ${api_err} == ''
      ;;gen;; Welcome, ${user_data.name}! Your location is set to ${user_location}.
    ;;else;;
      ;;gen;; Welcome! Could not fetch user details.
    ;;endif;;
  ;;endif;;
;;catch;; -err=e
  ;;gen;; An unexpected error occurred: ${e.message}
;;endtry;;

# List session variables for debugging
;;vars;; -scope=session


6. Implementation Notes
TokenLang is a specification. A compliant interpreter or runtime environment is required to execute TokenLang commands. This environment would handle parsing, variable management, command execution logic, permissions, and interaction with underlying systems (AI models, databases, file systems, network).
Crucially, for a system to correctly interpret and execute TokenLang commands, its underlying model or controller must be provided with this specification (or a compatible representation) as part of its operational instructions or knowledge base. This ensures the system understands the available tokens, their syntax (now ;;keyword;;), and the language's core rules. (Syntax updated)
7. Extensibility
The ;;def;; token provides a conceptual basis for defining custom commands or macros, allowing users to encapsulate common sequences of operations for reuse. (Syntax updated)
8. Future
