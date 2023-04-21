# Poly
AUTOSARC++14 rule docs


## M5-0-15 Bug
2021a complains in cases where array indexing is used.  
while 2022a does not generate any report
see : https://pvs-studio.com/en/docs/warnings/v2563/  
Base on this
https://jira.sonarsource.com/browse/RSPEC-941  
Violation hapens because not an array context
## M5-14-1
https://learn.adacore.com/courses/SPARK_for_the_MISRA_C_Developer/chapters/06_side_effects.html#:~:text=The%20reason%20is%20that%20the,rule%20forbids%20the%20code%20above.  
https://rules.sonarsource.com/cpp/RSPEC-912
# Common Weakness Enumeration
## CWE UNCAUGHT_EXCEPTION
Move asignment with containers might throw
* polymorphic allocators
* std::copy/memcpy
* performance
### std::basic_string<CharT,Traits,Allocator>::assign
https://en.cppreference.com/w/cpp/string/basic_string/assign
### Allocator propagation policies in your new modern C++ containers
https://stackoverflow.com/questions/54703727/allocator-propagation-policies-in-your-new-modern-c-containers
### Exception in move assignment operator
https://stackoverflow.com/questions/34398675/exception-in-move-assignment-operator
