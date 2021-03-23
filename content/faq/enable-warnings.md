---
title: "How do I enable compiler warnings?"
date: 2021-03-23T01:56:40Z
# weight: 1
# aliases: ["/first"]
tags: ["c", "c++", "dev-env"]
draft: false
disableShare: false
disableHLJS: false
searchHidden: false
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---


### For `gcc` and `clang` users
Add `-Wall -Wextra` to your compilation flags.

(Note that, contrary to what the name `-Wall` may imply, this does not enable *all* warnings, but it does enable most of the important ones.)

### For Visual Studio users
Unlike `gcc` and `clang`, Visual Studio does enable *some* warnings by default. To set the warning level to something different, Consult [Microsoft's documentation here](https://docs.microsoft.com/en-us/cpp/build/reference/compiler-option-warning-level?view=msvc-160#to-set-the-compiler-options-in-the-visual-studio-development-environment).
