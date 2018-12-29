## Package Review

*Please check off boxes as applicable, and elaborate in comments below.  Your review is not limited to these topics, as described in the reviewer guide*

- [x] As the reviewer I confirm that there are no [conflicts of interest](#coi) for me to review this work (If you are unsure whether you are in conflict, please speak to your editor _before_ starting your review).

#### Documentation

The package includes all the following forms of documentation:

- [x] **A statement of need** clearly stating problems the software is designed to solve and its target audience in README
- [x] **Installation instructions:** for the development version of package and any non-standard dependencies in README
- [x] **Vignette(s)** demonstrating major functionality that runs successfully locally
- [x] **Function Documentation:** for all exported functions in R help
- [ ] **Examples** for all exported functions in R Help that run successfully locally
- [x] **Community guidelines** including contribution guidelines in the README or CONTRIBUTING, and DESCRIPTION with `URL`, `BugReports` and `Maintainer` (which may be auto-generated via `Authors@R`).

>##### For packages co-submitting to JOSS
>
>- [x] The package has an **obvious research application** according to [JOSS's definition](http://joss.theoj.org/about#submission_requirements)
>
>The package contains a `paper.md` matching [JOSS's requirements](http://joss.theoj.org/about#paper_structure) with:
>
>- [ ] **A short summary** describing the high-level functionality of the software
>- [ ] **Authors:**  A list of authors with their affiliations
>- [ ] **A statement of need** clearly stating problems the software is designed to solve and its target audience.
>- [ ] **References:** with DOIs for all those that have one (e.g. papers, datasets, software).

#### Functionality

- [x] **Installation:** Installation succeeds as documented.
- [ ] **Functionality:** Any functional claims of the software been confirmed.
- [x] **Performance:** Any performance claims of the software been confirmed.
- [x] **Automated tests:** Unit tests cover essential functions of the package
   and a reasonable range of inputs and conditions. All tests pass on the local machine.
- [ ] **Packaging guidelines**: The package conforms to the rOpenSci packaging guidelines

#### Final approval (post-review)

- [ ] **The author has responded to my review and made changes to my satisfaction. I recommend approving this package.**

Estimated hours spent reviewing: 5.5

---

### Review Comments

Clearly much effort has gone into this package. I greatly support the sentiment behind making these data available in R having done the same for other data sets, myself. This package should be a great benefit to researchers in this area. I really appreciate the slim dependencies. It will make this package much easier to maintain into the future.

I found the package to be well documented, the vignette is helpful in illustrating some use cases for the data along with how to access it and the code is clear.

Some of the functionality I find to be mildly confusing like downloading and then importing the files into the R session and then formatting. As a user I'd prefer it all in one step, but there are likely use cases I'm not aware of that mean that this is useful so some examples of this would be useful I think.

My general comments on the code follow and sections for the DESCRTIPTION and Vignette as well. I've commented quite a bit on grammar and spelling as I think that the polish of a package is important as it lends to the perception of the quality.

Thanks for inviting me to review this. It's been fun and I've learned some new things.

--------------------------------------------------------------------------------

- Per rOpenSci policy, avoid start-up messages, rely on the documentation for
citation information:
https://ropensci.github.io/dev_guide/building.html#recommended-scaffolding.

- Avoid long lines >80 chars

- Inconsistent use of white spaces in code, see `find_file_name()` `if` statements for examples.

- The package does not pass `R CMD check`. *curl*, *readxl* and *tibble* are all listed as an Imports in DESCRIPTION but not imported from. With *curl* being used in tests, this means it should be in Suggests, I think. The others should be removed.

- I don't think it's good form to have an example that won't work on Windows in the help file for `stats19_schema`, from data.R - line 17? Most of what I see there would be better served in a `data_raw` folder showing how the data were created with the documentation actually documenting what the variables are not how they were created, see <http://r-pkgs.had.co.nz/data.html> and for an example, <https://github.com/ropensci/GSODR/tree/master/data-raw>.

- I would suggest to use proper formatting in help files, when naming packages, e.g. \pkg{stats19} and when referring to documented functions or data, e.g. \code{\link{stats19_schema}}, or with single quotes around abbreviations, e.g. 'DfT'. @ColinFay has an excellent page that outlines the formatting options and when/how to use them, <https://colinfay.me/writing-r-extensions/writing-r-documentation-files.html>. This will greatly enhance the users' experience when using the help files by making them more readable.

- I also would suggest making use of `@seealso` in documentation. For example, the `dl_stats19()` example works great in the help files, but from there I have the data but it's not in R. Using the `@seealso` you can let the user know about the `read_*()` functions.

- I downloaded files using `dl_stats19()`, selecting "Casualties", and then ran `read_accidents()` and got 
```r
Error in check_input_file(filename = filename, type = "accidents", data_dir = data_dir,  : 
  Change data_dir, filename, year or run dl_stats19() first.
```
Is it possible to be more descriptive and say that I've used the wrong `read_*()` based on the file/data found and offer to import it?

- Missing "." after "e.g." in dl.R on lines 8 and 9, there may be others that I didn't spy.

- Capitalisation in help files is inconsistent, e.g. lines 123-125 of read.R, parameter descriptions are mixed upper and lower case for first word after parameter itself. This applies to other functions where the descriptions are given in all lower case for other functions or upper case.

- Testing the functionality, I get this, when I expect it to tell me that `deaths` is not a valid input. But then when I hit escape, I expect it simply exit, not provide a warning message on the way out as well.
```r
dl_stats19(year = 1979, type = "deaths")
No files of that type found for that year.
This will download 240 MB+ (1.8 GB unzipped).
Files identified: Stats19-Data1979-2004.zip

Download now (y = enter, n = esc)? 

Warning message:
In find_file_name(years = year, type = type) :
  Coordinates unreliable in this data.
```

- I got caught out when using the interactive features. I read "y = enter" but hit "y" thinking that would work as well as hitting "enter", but R cancelled the operation anyway just as if I'd hit "esc"

- Per a recent conversation with CRAN, you should use `donttest()` rather than `dontrun()` for examples you don't want to be run on CRAN. Then set .travis.yml to run them by using `r_check_args: --as-cran --run-donttest`.

- When validating user inputs and using `stop()` it's nice to use `call. = FALSE` to simplify the error message that the user receives.

- Consider using [`hoardr`](https://ropensci.github.io/hoardr/) for managing user-saved files on disk that aren't in `tempdir()`?

- When using `utils::download.file()`, you should use `mode = "wb"` or Windows users may end up with corrupted downloads in my experience. `curl::curl_download()` does the same thing but uses more updated ways of doing it and defaults to using a binary mode (wb).

- I don't think that there is much need for the `Attempting download from` or `Reading in: ` message. If it takes that long, I would suggest to use a progress bar to show progress. But this is just a personal observation.

- Consider setting up a `pkgdown` site? It's easy to do and you can automate deployment with your Travis-CI so it's less to remember.

#### Tests

- I'm unclear how the interactive portion of the package functions is handled in testing? There are ways to handle this, but I don't see any implemented and when I run `devtools::test()` I'm asked to provide my own input.

- Suggest using `skip_on_cran()` since some of the tests can take some time to execute due to download times.

#### DESCRIPTION File

- In the DESCRIPTION file, Mark's author entry is missing his ORCID.

- More information in the DESCRIPTION's Description field would be desirable, a link to the data's website or other information to give more background perhaps.

- STATS19 should be in "'" in DESCRIPTION for CRAN, i.e., 'STATS19', I think.

- Check spelling in DESCRIPTION file, see: "analysie"

- The Description should include a link to the DfT website.

- Language field should be set, `Language: en-GB`

#### README File(s)

- Use `remotes::install_github()` in place of `devtools::install_github()` in README.

- The code style is inconsistent in the README.Rmd file in the code chunks, e.g. line 85 is missing space around `=`.

- The example in the README showing two steps seems necessarily confusing to new users. If there is a good reason for having the raw data in R, document in a vignette why this is useful and show the two-step process, but if normal users won't do this, I wouldn't show it in the quick-start.

- Line 43 of README uses inconsistent "(" around the phrases with the other two `read_*` function description.

#### Vignette

- Run spell-check on it.

- The term "attach"" has a specific meaning in R. Suggest rewording the portion about installation and loading the package to omit the use of "attach", since you're not using `attach()` in the R sense (and really shouldn't use it anyway).

- I would describe why a user might want or need to install the Development version from GitHub in the vignette. Presumably if they are reading the vignette, they've already installed the package from CRAN (in the future).

- Try to consistently use `function()` to identify functions in the vignette text. This also means that if/when you use pkgdown to build a site, the functions are linked to the help file.

- In the introduction, the description of why there are `read_*()` and `format_*()` functions is confusing. To me, it reads as if `format` is only a parameter for `read_*()` in the introduction. I was left wondering why it's documented there or why the `format_*()`s even exist until I reached the end of the vignette.

- There is a comma out of place in Vignette,

>Format: Each of the read_*() functions has a format parameter which, when TRUE, adds

should be 

>Format: Each of the read_*() functions has a format parameter, which, when TRUE, adds 

- I'm unsure about including a package that's not on CRAN in the vignette (`ukboundaries`), something like this should be listed in Suggests, but it's not on CRAN, @sckott do you have any thoughts?

- The first figures in the `sf` section after the join aren't immediately clear to me. The axis lack labels, I'm not really sure what I'm looking at.

#### Meta

- The contributing guidelines mention a `pkgdown` website, this does not exist
