# cmake ninja commands
```
env CC='which clang' CXX='which clang++' /
env CC=/usr/bin/clang CXX=/usr/bin/clang++

sudo apt-get install -y lld

cmake -DLLVM_USE_NEWPM=ON -DCMAKE_BUILD_TYPE=RelWithDebInfo -DLLVM_ENABLE_ASSERTIONS=ON -DLLVM_TARGETS_TO_BUILD="X86" -DBUILD_SHARED_LIBS=ON -DLLVM_ENABLE_PROJECTS="clang;clang-tools-extra" -G "Ninja" -DLLVM_USE_LINKER=lld ../llvm

ninja -j2 all

ninja clang-tidy
```
# clang-tidy 
./clang-tidy -checks=-*,misc-my-first-check testfile.cpp --

./clang-tidy -checks=-*,misc-my-first-check -fix testfile.cpp --


## clang-query
### set bind-root false
### set traversal     IgnoreUnlessSpelledInSource
### set output dump
### set output diag

### Exploring Clang Tooling, Part 0: Building Your Code with Clang
https://devblogs.microsoft.com/cppblog/exploring-clang-tooling-part-0-building-your-code-with-clang/

### Exploring Clang Tooling Part 1: Extending Clang-Tidy
https://devblogs.microsoft.com/cppblog/exploring-clang-tooling-part-1-extending-clang-tidy/

### Exploring Clang Tooling Part 2: Examining the Clang AST with clang-query
https://devblogs.microsoft.com/cppblog/exploring-clang-tooling-part-2-examining-the-clang-ast-with-clang-query/

### Exploring Clang Tooling Part 3: Rewriting Code with clang-tidy 
https://devblogs.microsoft.com/cppblog/exploring-clang-tooling-part-3-rewriting-code-with-clang-tidy/

### Location, Location, Location
https://steveire.wordpress.com/2021/04/27/location-location-location/

### Future Developments in clang-query
https://steveire.wordpress.com/2018/11/11/future-developments-in-clang-query/

### Using clang-query
https://firefox-source-docs.mozilla.org/code-quality/static-analysis/writing-new/clang-query.html


# Matcher notes
```
m functionDecl().bind("x") 
```
this will prevent header files code form being parsed and lots of "unrelated" Matchers showing up
```
m functionDecl(isExpansionInMainFile()).bind("x") 
```
functionDecl is too generic and will match

CXXDestructorDecl 
CXXMethodDecl 
CXXConstructorDecl 
CXXMethodDecl 

```
m namedDecl(isExpansionInMainFile()).bind("x") this is even more general, and it will match every declararion
```
CXXRecordDecl 
FieldDecl 

CXXRecordDecl   FieldDecl 
```
m cxxRecordDecl(hasDescendant(fieldDecl(isExpansionInMainFile()).bind("classFieldDeclaration"))).bind("classDeclaration")
```
### it will match each class field declaration
```
m fieldDecl(isExpansionInMainFile(), hasParent(cxxRecordDecl().bind("classDeclaration"))).bind("classFieldDeclaration")
m fieldDecl(isExpansionInMainFile(), hasParent(cxxRecordDecl())).bind("classFieldDeclaration")
```
### it will match each class NSDMI field declaration
```
m fieldDecl(isExpansionInMainFile(), hasParent(cxxRecordDecl()), has(expr())).bind("classNSDMIFieldDeclaration")
m cxxRecordDecl(isExpansionInMainFile(),  	forEach(fieldDecl(hasParent(cxxRecordDecl()), has(expr())).bind("nsdmi-member"))).bind("class")
```
### it will match each class No NSDMI field declaration
```
m fieldDecl(isExpansionInMainFile(), hasParent(cxxRecordDecl()), unless(has(expr()))).bind("classNoNSDMIFieldDeclaration")
m cxxRecordDecl(isExpansionInMainFile(),  	forEach(fieldDecl(hasParent(cxxRecordDecl()), unless(has(expr()))).bind("member"))).bind("class")
```

# test_class1.cpp
```
#include <array>
#include <iostream>
#include <string>

class Test {
public:
  class X {
    int op1() {
      int r;
      r = int_xa_nsdmi;
      int_xb_nsdmi = int_xa_nsdmi;
      return r;
    }
    int int_x_var;
    int int_xa_nsdmi{2};
    int int_xb_nsdmi = 1;
  };

  std::string ops() {
    std::array<int, 3> array_nsdmi_var;
    if (array_nsdmi_var.at(0)) {
      return "OK";
    } else {
      return "XX";
    }
  }
  int getIntVar() const { return int_var_; };

  bool getBoolVar() const { return bool_var_; };

  std::string getString() const { return string_var_; };

  std::array<int, 3> getArray() const { return array_var_; };

private:
  int int_var_;
  bool bool_var_;
  std::string string_var_;
  std::array<int, 3> array_var_;

  int int_nsdmi_var_{0};
  bool bool_nsdmi_var_ = true;
  std::string string_nsdmi_var_{"lola"};
  std::array<int, 3> array_nsdmi_var_ = {1, 2, 3};
};
```
