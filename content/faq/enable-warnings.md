---
title: "How do I enable compiler warnings?"
tags: ["beginner", "dev-env"]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
---

### For `gcc` and `clang` users:
**Add `-Wall -Wextra` to your compilation flags.**

Other useful options include:
* `-Werror`: treats warnings as if they were errors
* `-pedantic`: generates warnings if you use any language extensions
* `-Wno-x`: disables warning `x`, e.g if the compiler gives you a `-Wunused-variable` warning, you can turn it off with `-Wno-unused-variable`

(Note that, contrary to what the name `-Wall` may imply, this does not enable *all* warnings, but it does enable most of the important ones.)

### For Visual Studio users:
Unlike `gcc` and `clang`, Visual Studio does enable *some* warnings by default. To set the warning level to something different, Consult [Microsoft's documentation here](https://docs.microsoft.com/en-us/cpp/build/reference/compiler-option-warning-level?view=msvc-160#to-set-the-compiler-options-in-the-visual-studio-development-environment).
