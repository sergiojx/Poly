# Poly
AUTOSARC++14 rule docs


## M5-0-15 Bug
2021a complains in cases where array indexing is used.  
while 2022a does not generate any report
see : https://pvs-studio.com/en/docs/warnings/v2563/
Base on this
https://jira.sonarsource.com/browse/RSPEC-941
Violation hapens because not an array context
