Type the following commands to run Fortify Static Code Analyzer on source.php and sink.php:

$ sourceanalyzer -b sample-php -clean

$ sourceanalyzer -b sample-php source.php sink.php

$ sourceanalyzer -b sample-php -scan -f sample-php.fpr

As with all scripting languages, it is important to translate (the second command that converts source files into analyzable format) as many of the input source files as possible at one time. This enables the translation phase to glean more data from the original sources. In languages such as C or Java, the source files contain explicit information about types and externally referenced code and data, which we must infer for scripting languages.

Open the analysis results file in Audit Workbench:

$ auditworkbench sample-php.fpr

This test demonstrates a couple of simple dataflow vulnerabilities, and
one dangerous function. While it is artificial, it illustrates the types
of problems PHP programs frequently experience. The program reads data from
the $_GET array (which contains data provided by the -- potentially
hostile -- browser) into the variable $tainted. This data is added to
and modified, and copied into the variable $my_var.

This variable, which still contains potentially dangerous data from an
attacker, is passed to another function, do_work(), which is in the
sink.php file.

This function does two dangerous things with the arguments, which it
evidently trusts:

      - Prints out a string, which will normally wind up on a
        web page. An attacker carefully placing hostile data
        in a URL can thus control this web page.
      - Runs a SQL query. An attacker placing hostile data in
        a URL can perform essentially any database operation.

The first issue is a cross-site scripting (XSS) attack, and the second
is a SQL Injection attack. Both of these issues should be shown in
Audit Workbench.

In addition, a third issue, Possible Variable Overwrite, should also
exist. A function in sink.php, do_other_work(), which is never called
nonetheless uses the extract() builtin function. The extract() function essentially
cannot be used safely, because its purpose is to overwrite variables.

The Fortify analysis might detect other types of issues depending on the Rulepack version used
in the scan.
