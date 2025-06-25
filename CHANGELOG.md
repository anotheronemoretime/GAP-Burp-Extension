## Changelog

- v5.8

  - New

    - Get links when jQuery-like methods such as `.get()`, `.post()`, etc., are being used. There are some false positives by just checking for `.get(` instead of `$.get(` for example, but the trade off is worth it for now.
    - Get links from `fetch()` just in case there are any that the main regex does not get.

  - Changed

    - Potential parameter names should only be displayed if they ONLY contain the following characters `A-Z a-z 0-9 - _ . ~ [ ]`. These are the only characters that should appear in parameter names.

- v5.7

  - New

    - Also get potential links from S3 bucket file listings where file names can be in `<Key></Key>` tags.

  - Changed

    - BUG FIX: Prefix any extra links found with `tldextract` with a `//` so they are treated later as full URLs, not file names, and therefore checked correctly for scope.

- v5.6

  - Changed

    - If the `cbParamInputField` checkbox is selected then it will get names from HTML `input` fields AND `textarea` fields. If the content type contains `HTML` OR `JAVSCRIPT` then the response will be searched (because the HTML may be getting built in the javascript code).

- v5.5

  - New

    - Add a `COMMON_TLDS` list. This can't be edited through the UI at the moment, but can be changed in the code if required.
    - Perform a separate regex search for links that can potentially get lots of false positives, but validate these by checking the TLD for the link is valid. The following will NOT be returned:
      - invalid domains (TLDs that don't exist)
      - domain with less than 3 chars before tld
      - domain doesn't start with `_`
      - suffix `call`,`skin`,`menu`,`style`,`rest`,`next`
      - domains `this`,`self`,`target`,`value`,`values`,`prop`,`properties`,`proparray`,`useragent`,`rect`,`paddiing`,`style`,`rule`,`bound`,`child`,`global`,`element`,`div`,`prototype`,`event`,`feature`,`path`
      - suffix is `js` and domain is NOT `map`
      - domains with TLDs that aren't in the `COMMON_TLDS` list
    - Before searching for links, replace different encodings of double quotes with `"` to maximise the number of links found.
    - Remove any base64 encoded strings over 10,000 characters long (that match regex `eyJ[a-zA-Z0-9\+\/]+(?:=|\b|\n)`). There are some responses that can have huge ones and ends up causing regex problems and hanging.
    - Don't include any links that start with a `-`.

  - Changed

    - Update the latest version of the Jython `.jar` file in `README.md` and `GAP Help.md`.
    - Update the `pip install` command in the installation instructions to use `--no-cache-dir --no-compile`. This resolved an issue I had trying to install `tldextract`.

- v5.4

  - Changed

    - BUG FIX: Change the `self.REGEX_LINKS` regex to also look for `.` within file names. If a file name is found that has a required extension, e.g. `.js` then the file name itself can also have a `.` in it, e.g. `chunk.1337.js`. Previously the regex would not have picked these up in certain situations.

- v5.3

  - Changed

    - Change `REGEX_JSNESTED` (used by the `Javascript variables and constants` option) to also get params from nested objects that start with `JSON.stringify(`.

- v5.2

  - Changed

    - Fix bug where "sus" parameters wouldn't be shown at all in the `Potential param` pane if the option to `Report "sus" params` issues was not selected.
    - Change `REGEX_JSNESTED` (used by the `Javascript variables and constants` option) to also get params from nested objects that start with `dataLayer.push(`.
    - Improve the description for `Javascript variables and constants` in the `GAP Help.md`.

- v5.1

  - New

    - When the `RESPONSE PARAMETERS` option of `Javascript variables and constants` is selected, GAP will now extract parameter names from nested objects too.
    - Add `Inc. Tentative?` option next to `Report "sus"params?` option. Findings marked `Tentative` will only be raised if this option is checked. If disabled it will speed up processing.

  - Changed

    - Move the check for Sus param checkbox being checked to the start of `checkSusParams` instead of half way through, to help speed things up.
    - Change regex `REGEX_PARAMSPOSSIBLE` and `REGEX_PARAMSSUB` to check for HTML entities with the `&` as optional, as sometimes they appear without. This ensures they are replaced more effectively.

- v5.0

  - New

    - You can right click on the Potential Links found list, and if `Show origin endpoint` is unchecked and `In scope only` is checked, you can select `Request all prefixed URLs and send to Site Map`. This will request all URLs shown in the current pane (so can be filtered with `Link filter` too) and make a GET request for each URL. The URLs will be requested in 2 separate threads, with 10 milliseconds between each request, and they will be added to the Site Map.
    - When requests are being made, you can right click on the Potential Links found list and select the `Cancel all requests being made` menu item to stop all requests previously scheduled.
    - If a link found ends with a `.` or `|`, then they will be removed.
    - If a link found has literal `\n` or `\r` in it, then it will be stripped from the first occurrence.
    - When checking if a link is valid, check if it has a host and it is a valid host. This will remove false positives like `alert(e.which)` for example.

  - Changed

    - Change code that looked for a closed bracket without an open one, for better code that checks for unbalanced brackets, and strips from the unbalanced closing bracket.
    - Change `getResponseLinks` to prevent runtime errors happening when the link gets set to blank.
    - Remove a print statement accidentally left in when copying to the clipboard.

- v4.9

  - New

    - You can click the progress bar to see a modal dialog box showing the datetime of the last run, with the context GAP was run from (e.g. `Target Site Map Tree`, `Proxy History`, etc.) and a list of Hosts. For all contexts other than `Target Site Map Tree`, there will be a `Show hosts only` check box (defaults to checked). If unchecked, then a list of unique target URLs are displayed instead.
    - You can now right click on the params, words and links pane and click the `Copy` link to copy the contents of that pane to the clipboard. For "sus" parameters view, the parameters will be copied without the vulnerability types in square brackets after them.
    - If GAP is called from the Site Map tree context, then Scope has to be set to work. This can often make people think GAP isn't working correctly. So, if run from this context and no content is found, and additional message is given in the output pane(s) saying `Maybe scope isn't set? It needs to be set to call GAP from the Site Map tree. Ignore this if there are results for other modes.`

  - Changed

    - Change the progress bar tooltip text to `Click to see execution scope. When running, this shows what request is being processed out of the total number of requests for current target.`

- v4.8

  - New

    - Add mime types `image/jp2` and `video/x-m4v` to the `CONTENTTYPE_EXCLUSIONS` list.

  - Changed

    - BUG FIX: When the context is NOT Site Map, and the `Prefix with selected target(s)` option is selected, it did not output any URLs that did not have a domain. The URLs domain is now used, and all links output correctly.
    - BUG FIX: When a URL is entered in the `Prefix with links(s)` field, it is highlighted in red for an invalid URL, bu the regex used is incorrect. This has been replaced wit a more effective regex.

- v4.7

  - Changed

    - When more that one target is selected and the `Auto save` option is selected, GAP attempts to get the Burp project name from the root window to write the accumulated files. If it failed to get the project name, it raised an error and failed to create the files. This bug has been fixed. If GAP is unable to get the project name, it will just write the accumulated file names with `UnknownProject_`.

- v4.6

  - New

    - Added `video/x-ms-wmv,image/x-png,video/quicktime,image/x-ms-bmp,font/opentype,application/x-font-opentype,application/x-woff,audio/aiff` to the list of MIME types to ignore.
    - If a parameter value has a backslash, forward slash, `quot;`,`apos;` or `amp;` in it, they will be removed.

  - Changed

    - After the new footer has been added in the recent version of Burp, some of the GAP controls are covered on a standard display. I have moved things around a bit to ensure they are all visible.
    - The functionality to remove the logo header with Ctrl-Click has been removed as is no longer relevant.

- v4.5

  - New

    - Added `application/x-msdownload` and `application/x-ms-application` to the list of MIME types to ignore.

- v4.4

  - Changed

    - Added `application/font-otf` to the list of MIME types to ignore.

- v4.3

  - New

    - Add new Sus parameter group for Mass Assignment as requested in https://twitter.com/nullenc0de/status/1700496197570453700?s=20

- v4.2

  - Changed

    - Change the test displayed in the Words mode text box if the `beautifulsoup4` module could not be loaded. This will include the installation instructions.

- v4.1

  - New

    - Add `application/x-font-truetype`, `font/opentype`, `application/x-troff-man` and `image/pjpeg` to `CONTENTTYPE_EXCLUSIONS`.
    - Add file extensions `.jfi`,`.jfif`,`.jfif-tbnl`,`.jif`,`.jpe` and `.pjpg` to `DEFAULT_EXCLUSIONS` and `FILEEXT_EXCLUSIONS`. These relate to `image/pjpeg`.
    - If debug view is shown then also output the content types that are being processed. This is helpful for myself to add new content-types that should be excluded.
    - Only get words from a response if it is a content-type/file that we want to process. This check is done for links and parameters bit wasn't being checked for words.

  - Changed

    - When calling the Burp API callbacks `isInScope` method, don't call if the host without scheme isn't valid, e.g. doesn't match regex `^([A-Za-z0-9_-]+\.)+[A-Za-z0-9_-]{2,}$`. This prevents errors that an occur in the Burp API that crashes GAP.
    - The `FILEEXT_EXCLUSIONS` list is only checked if the Content-Type of a response isn't found. But there are sometimes cases where the Content-Type of a response is incorrect. Both will be checked to determine whether to get links from a response.
    - Change the call `soup.findAll(text=lambda` to `soup.find_all(string=lambda` because `find_all` should be used instead of `findAll` and `text` has now been deprecated and raises a warning.
    - When checking tag properties for values, the default was set to Null if it can't get the value, so may cause an error. The default changed to an empty string.
    - If a link is found and the last character is `=` or `:`, then remove it.

- v4.0

  - New

    - Added `Report "Sus" parameters?` option for Parameters mode.
    - Added `Show "sus"` option for Parameters mode.
    - Raise a Burp custom Issue (not available in Community Edition) if a "Sus" parameter is identified.
    - For Community Edition, write details of "Sus" parameters found to the extension output.
    - Added `Create lowercase words?` Word option to determine whether to add a lowercase version of a word if it contains any uppercase letters. In previous versions this was done by default.
    - Added context help in the form of tool tips on most features and controls of GAP.
    - Added `Show context help` option to turn off tool tips the next time GAP is loaded.
    - Added `Include relative links?` Parameter option to determine whether to include links in the results if they start with `/./` or `/../`
    - Added functionality to get Parameters from the Request that Burp doesn't successfully detect. Initially this will just get parameters within JSON strings.
    - Do not get words from a `*.js.map` file. Sometimes these are JSON rather than javascript and end up adding a lot of pointless words like mapping names.
    - Ignore certain words if found in `robots.txt`
    - Do not include words that are in paths. A lot of these were previously being included even if the `Include URL path words?` option wasn't selected because of the regex to get words was not good enough.
    - If you hold down the Ctrl key when clicking the GAP logo header image, it will be removed. This can be used in cases where some controls are not visible on the screen.
    - Added the regex part `(\"|\')([A-Za-z0-9_-]+\/)+[A-Za-z0-9_-]+(\.[A-Za-z0-9]{2,}|\/?(\?|\#)[A-Za-z0-9_\-&=\[\]]*)(\"|\')` to the main Link finder regex to get more potential links. Also, ignore any links that then start with `application/`, `image/`, `model/`, `video/`, `audio/` or `text/` because these are content-types that can be confused with links.
    - Add `wasnt` to Stop Words list.
    - Add `.pdf` to `FILEEXT_EXCLUSIONS` constant.
    - Ignore links if they start with `/=` (some false positives).
    - When running GAP from the Site Map tree context, the progress bar will say `Getting reqs...` when it is getting the number of requests for the current target.
    - When encoding links to display, set `:` and `/` as `safe`. Also replace the value `%C3%83%C2%82%C3%82%C2%A0` which can sometimes occur when the character `Â` occurs instead of `&nbsp;`.

  - Changed

    - Rewrote the `doeverything` function to deal with the Site Map tree context differently to the other contexts, to improve performance.
    - If you start GAP from the Site Map tree, only in scope requests will be checked.
    - Removed the `Include common parameters?` check box. This functionality has been removed, partially because of it's limited use, and also to allow for space to add sus parameter options.
    - Remove the trailing `&` off the end of the parameter query string.
    - If the `Include site map endpoints?` option is selected, then also include URLs of requests in the site map that haven't been requested yet (i.e. found by Burp in crawling), if they are in scope.
    - Fix a bug that was including a potential link that had no scheme (e.g. a file name) with AND without a prefixed `/`
    - Suppress errors in `addLink` when doing `urlparse`. If an error occurs then the URL will be ignored.
    - Only display parameters that contain at least one letter, number or \_
    - Improve regex to get more parameters from the response that could be parameters in encoded links
    - Display the progress bar as soon as GAP is started so it is clearer it is running if it takes time to get all roots and messages
    - Improve the regex for finding links in the responses
    - Don't display potential links if they contain any unprintable ASCII characters (0-31)
    - Remove the check for `X-SourceMap` because it is already covered by the existing regex
    - Improve performance initially when getting the number of messages selected to display in the progress bar
    - Improve `sanitizeWord` function to use regex and also remove spaces and %20. Also correct error not replacing %29
    - If the Parameter or Word mode `Include URL path words?` option is selected, only get if there is a response. This is because Burp will put links in the sitemap that haven't been requested, but can incorrectly get links with wrong paths that then end up with words that make no sense.
    - Get potential words from more `meta` tags, and also get from some relevant `link`-`rel` tags.
    - Remove the `Name attribute of meta tags` Parameter option because this has little to no value at all.
    - Show relative links without being prefixed with `/`
    - Pull request from [bebiksior](https://github.com/bebiksior) to improve word list by splitting words with dash, and also by comma.
    - Replace regex `findall`and `search` with pre-compiled statements for better performance.
    - Fix logic in `includeContentType` where unnecessary calls were being made. Also just call `includeContentType` once at the start and use the result later instead of calling 3 times.
    - Remove `robots.txt` in `DEFAULT_EXCLUSIONS` (not sure why I put it in there in the first place!)
    - Change `polyfill.io` to `polyfill` in `DEFAULT_EXCLUSIONS`
    - Resolve an issue that can cause CPU to max out if the `Prefix with selected Target(s)` option is selected, and there are a lot of targets selected with many links.
    - Remove the test `(?<=\=)\s*\/[0-9a-zA-Z]+[^>\n]*` from the response link Regex because it gives too many false positives and can also end up selected a huge part of JS files and cause performance issues.
    - Make a change to the Link regex to make sure that potential links that start with `//` are not followed by any spaces.

- v3.5

  - Changed

    - Fix a bug that fails to process a root in the site map if it contains a port number.

- v3.4

  - Changed

    - Fix a bug that was causing the `Show params as query string` option to not display anything if the `Include common parameters` option is not ticked.
    - Ensure there is a newline character at the end of the final line on output files.

- v3.3

  - New

    - Allow GAP to be called from any context, not just the site map.
    - Add a `Link exclusion` check box. If this is unselected then ALL links wil be returned without checking the exclusion list.

  - Changed

    - Fix a bug where links that are out of scope are still shown when the `In scope only` option is selected.
    - Fix a bug where unicode characters weren't always being converted correctly so some links may not have been successfully extracted.
    - Fix a bug where if a link of `*.example.com` was found then it would be reported as `http://.example.com`
    - Fix a bug where links with something between parenthesis or curly brackets in part of the subdomain stops the link being identified as being in or out of scope.
    - FIx a bug where if a field isn't in the saved config, and exception occurs that prevents the remaining fields being set. Now if an error occurs getting any field from the saved config, it will set a default and continue.

- v3.2

  - Changed

    - Amend the main link finding regex string to avoid catastrophic backtracking errors that freeze the search and break GAP.
    - Fix a bug where the links `Show origin endpoint` and `In scope only` options aren't enabled in some circumstances.
    - Ensure output files are closed properly after writing.
    - Fix a bug where the `Show origin` of the wrong type was checked when writing output files.

- v3.1

  - Changed

    - A small fix to ensure that if the `Prefix with selected Target(s)` option is checked that output links do not have `//` after the host

- v3.0

  - New

    - Add `Show origin endpoint` filter to Parameters and save as part of config when the **Save options** button is pressed.
    - If the `Auto save output to directory` option is checked, then files are written as follows:
      - Create a sub folder for each root in the target site that was selected in Site Map
      - Create a file in the main folder with the name of the Burp project and timestamp, e.g. if the project is called `target`, the files might be `target_20230416_133700_links.txt`, `target_20230416_133700_parans.txt` and `target_20230416_133700_words.txt` where `20230416` is the current date in `YYYYMMDD` format and `133700` is the current time in `HHMMSS` format. These files will contain what was found for ALL roots selected.
      - Within each sub folder, the files will only contain findings for that particular root. If there are no findings for a mode, then a file will not be created for that mode.
    - If the `Include common parameters` option is checked and the Parameter `Show origin endpoint` is checked, the common parameters are displayed and written to file with `[GAP]` instead of a Link where the parameter was found.

  - Changed
    - You can now select sub folders, or specific requests, from the Site Map to process with GAP.
    - The `Show origin endpoint` option for Parameters and Links, and the `In scope only` option are applied to what is displayed AND what is written to file.
    - If a link is found that has a different scheme than http or https, the `URL(link).getHost()` method returns blank. This previously resulted in the link not being checked if it was in scope and incorrectly included. This has been fixed.
    - Make changes to the functions that display links, parameters and words to improve speed and use less memory.
    - Parameters and words from path words were not correctly checked whether they are in scope or not. This has been fixed.
    - If a link has `\s` or `\S` in it, don't include as it's most likely a regex string, not a link.
    - If one mode finishes before others are, allow any filters to be used on the finished panels, even if the others aren't complete.
    - If Origin is written to the Links or Params file, only separate the URL and \[ORIGIN\] with one space. Two spaces are used in the UI to make it easier to view.
    - Suppress warnings from the beautifulsoup4 library.

- v2.9

  - New

    - Add new checkbox "Prefix with selected Target(s)". If selected, any links found that don't have a domain will be prefixed with each target root that was selected in the Site Map when running GAP.
    - Added some tool tips

  - Changed

    - The "Prefix with links(s)" (was previously called "Link Prefix") can now have multiple links separated by a semicolon. If a schema is left off a link then it will be added on. If the field has invalid values, the text will be displayed in red, indicating it needs to be fixed. Links will be output with each prefix

- v2.8

  - New

    - When GAP is searching, the tab caption will say **GAP\*** instead of **GAP**. Also, when complete, and the user is not on the GAP tab, the title will show **GAP** in Burp Orange. The text is reset to default colour when another target is searched of if any options are changed.

- v2.7

  - New

    - Sanitize words before adding them to the list, e.g. remove `"`,`%22`, `<`, `%3c`, etc.
    - If a potential parameter has a `?` in it, then just get the value after the `?` as the parameter

  - Changed

    - Add more parameter names to the `COMMON_PARAMS` constant.

- v2.6

  - Changed

    - For Parameters, Links and Words, check if the string being added contains any unicode characters. If it does, then URL encode the characters before adding them to the lists to display and output. This change prevents a number of errors output and also prevents Burp from freezing with certain conditions.

- v2.5

  - New

    - Get more potential parameters from responses based on patterns like `?param=` and `&param=`

  - Changed

    - Only get parameters from responses that don't have content types of file types in the given exclusions.

- v2.4

  - New

    - Add `FILEEXT_EXCLUSIONS` constant that are file extensions we do not want to check for links. If a content type cannot be found then the extension in a URL (if there is one) will be used to check against this list and exluded if necessary.

  - Changed
    - Add these content types to the `DEFAULT_CONTENTTYPE_EXCLUSIONS` constant, and the `contentExclude` section of `config.yml`: `application/zip,application/x-zip-compressed,application/x-msdownload,application/x-apple-diskimage,application/x-rpm,application/vnd.debian.binary-package`

- v2.3

  - New
    - Re-introduce the option of viewing parameters in a concatenated query string. There is now a check box below the parameter list that can be used to switch views.

- v2.2

  - Changed
    - Encode parameters, links and words to ASCII before adding them to lists so that no unicode errors occur when displaying them.
    - Change error message for bs4 not installed to include a link to the installation instructions on github.

- v2.1

  - Changed
    - Minor bug fix and improvement

- v2.0

  - New

    - Add **Words** mode that will produce a target specific wordlist.
    - Add options for **Words** mode.
    - Add an option to provide a prefix for links that are found that don't have a domain.
    - Add `requirements.txt` file for external modules that are needed for GAP.
    - Add a progress bar to show how many requests per root are being processed.
    - Add a **Buy Me a Coffee** button.
    - Add `banner.png` to use on extension tab.

  - Changed
    - Sooooo many minor bug fixes to mention :)
    - Allow user to select a sub folder of a site mop root, or even just one endpoint to process.
    - Get links from the response headers too. It should have been doing this already, but wasn't.
    - When **Include site map endpoints in link list** option is selected, return the full URL, not just the path.
    - Fixed bug when saving files on Linux.
