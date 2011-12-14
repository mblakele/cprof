CPROF - Conditional Profiling for MarkLogic Server
===

Conditional Profiling
---

You have written an application or a service using MarkLogic Server
and XQuery or XSLT. Your application uses some combination of
HTTP, `xdmp:eval`, `xdmp:invoke`, `xdmp:value`,
`xdmp:xslt-eval`, and `xdmp:xslt-eval`.
You have discovered a slow request, and you want to profile it.
But adding profiler support to an HTTP request can be tricky.
Changing metaprogramming calls like `xdmp:invoke` to `prof:invoke`
is also tricky: the functions from the
[Profile API](http://developer.marklogic.com/pubs/5.0/apidocs/ProfileBuiltins.html)
return a sequence of the ordinary results, followed by a profile.
This breaks your existing code.

What you want is a way to profile your main request,
and stack up nested evaluation profiles until the end of the query.
Then you can decide what to do with all the profiler output:
display it, log it, email it, etc.

This XQuery library makes that pattern easy. Here is an example:

    import module namespace cprof="com.blakeley.cprof" at "/path/to/cprof.xqy";
    if (xs:boolean(xdmp:get-request-field('profile', '0'))) then cprof:enable()
    else (),
    cprof:value('xdmp:sleep(5)'),
    cprof:report()

The only necessary logic is whether or not to call `cprof:enable`.
After that, you can simply replace `xdmp:eval`, `xdmp:invoke`, etc
with `cprof:eval`, `cprof:invoke`, etc.
The function signatures are identical.
At the end of the query, call `cprof:report`
and do whatever you like with the sequence of `prof:report` elements.
If you get back the empty sequence, then profiling was not enabled.

Note that `cprof:report` takes an optional boolean argument.
If set, the return value will be a single `prof:report` element
with the main request's `prof:metadata` element and with
all of the histograms merged into one `prof:histogram` element.
This may be a little confusing, since the sum of
all the histogram expressions may exceed headline elapsed time.

The functions in this library are lightweight,
so you do not need to disable them for production.

The cprof test cases use [XQUT](https://github.com/mblakele/xqut).
If you find problems, please provide a test case.
Patches are welcome.

License
---
Copyright (c) 2011 Michael Blakeley. All Rights Reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

The use of the Apache License does not indicate that this project is
affiliated with the Apache Software Foundation.
