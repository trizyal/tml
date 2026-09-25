# tml (Maths Utility Library)

**tml** is a lightweight, header-only C++20 utility library designed to solve common modern C++ headaches—specifically, the friction between signed 32-bit integers (common in game development and formats like glTF) and unsigned 64-bit `size_t` containers (like `std::vector`), while enforcing strict compiler warnings without spam.

## Features

* **`tml::Index32`**: A zero-overhead, strongly-typed 32-bit index wrapper.
* Eliminates `-Wsign-conversion` warnings.
* Safely downcasts `size_t` bounds.
* Natively supports C++20 spaceship (`<=>`) comparisons.
* Functions perfectly as a loop iterator (`++i`) and implicitly converts to `size_t` for direct array/vector access (`myVector[idx]`).
* Provides strict runtime bounds-checking (prevents negative indexing).


* **`Ensure / Fatal`**: Robust assertion macros (`ENSURE` and `FATAL`) that hook directly into hardware breakpoints (`__debugbreak()` / `__builtin_trap()`) to halt execution exactly where a failure occurs in the debugger, providing clear file and line number logging.

---

## Installation

**tml** is built as a modern CMake `INTERFACE` library. The easiest way to include it in your project is via a Git submodule.

**1. Add the submodule:**

```bash
git submodule add https://github.com/trizyal/tml.git external/tml
git commit -m "Add tml submodule"

```

**2. Link via CMakeLists.txt:**

```cmake
# Add the directory containing tml
add_subdirectory(external/tml)

# Link it to your executable or library
target_link_libraries(YourProjectName PUBLIC tml)

```

---

## Usage

### Strongly-Typed Indices (`index32.h`)

Graphics APIs and formats (like glTF) heavily rely on signed 32-bit integers (`int`), but standard C++ containers use `size_t`. Mixing them triggers strict compiler warnings and risks silent underflows. `tml::Index32` solves this automatically:

```cpp
#include <tml/index32.h>
#include <vector>

struct Node {
    std::string name;
    tml::Index32 parentIndex; // Defaults to -1 (invalid)
};

std::vector<Node> nodes = GetNodes();

// Seamless iteration without static_cast<int> or -Wsign-conversion warnings
for (tml::Index32 i = 0; i < nodes.size(); ++i) 
{
    // Implicitly casts to size_t for safe vector access
    Node& current = nodes[i]; 

    // Safely compares against 0 without ambiguity
    if (current.parentIndex >= 0) 
    {
        // Use Index32 exactly like a normal integer
        Node& parent = nodes[current.parentIndex];
    }
}

```

### Assertions (`Ensure.h`)

Replaces standard `<cassert>` with debugging-friendly macros that print exact file/line locations and trigger IDE breakpoints.

```cpp
#include <tml/Ensure.h>

void processAsset(const Asset* asset)
{
    // Soft Assert: Logs an error and triggers a debugger breakpoint, 
    // but allows the program to continue running if the debugger is detached/skipped.
    ENSURE(asset != nullptr, "Asset pointer was null, skipping process.");
    if (!asset) return;

    // Hard Assert: Logs an error, triggers a breakpoint, and forcefully aborts the application.
    FATAL(asset->isLoaded, "Attempted to process an unloaded asset. Unrecoverable state.");
}

```

---

## Requirements

* **C++20** (Requires `<compare>` for the spaceship operator).
* **CMake 3.25+**

## License

Copyright (C) 2026 trizyal.
Licensed under the **Apache-2.0** license. See the source headers for details.
