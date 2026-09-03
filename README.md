# University of Nebraska - Lincoln Thesisdown Template

**Purpose**

Template for writing your UNL thesis with bookdown.

**To Use**
1. Fork this repository to a new Rproject hosted on your computer or download and extract the repository as a .zip file and create a new Rproject within the folder.
2. In RStudio, in the Environment/Git window pane, click on the **Build** tab. Then **Build Book.** This will take some time, especially for the first build. Let it go!
3. Your thesis document can be found at **docs/thesis.pdf**.

**To Build Without RStudio**

The **Build Book** button runs `bookdown::render_book()`. You can call it directly
from an R console or a shell in the project directory:

```r
# All output formats declared in index.Rmd
bookdown::render_book("index.Rmd")

# PDF only
bookdown::render_book("index.Rmd", output_format = "bookdown::pdf_book")

# Preview a single chapter while drafting
bookdown::preview_chapter("03-chap3.Rmd")
```

Output is written to `docs/` (set by `output_dir` in `_bookdown.yml`).

**Required R Packages**

`bookdown`, `knitr`, `rmarkdown`, `dplyr`, `ggplot2`, `devtools`, and `huskydown`
(installed from GitHub with `devtools::install_github("benmarwick/huskydown")`).
The setup chunks in `index.Rmd` and `03-chap3.Rmd` install anything missing on the
first build.

**Debugging Tips**
+ Make sure your R/RStudio is updated (>= version 4.0.0).
+ Make sure you have latex/MikTex on your computer. Can you knit a pdf?
+ You may need to follow the initial setup instructions at https://github.com/benmarwick/huskydown.

**Resources**
+ Modified from: https://github.com/benmarwick/huskydown.
+ University format guidelines: https://www.unl.edu/gradstudies/academics/degrees/guidelines/format#global

**Examples**
+ Download Emily A. Robinson (creator) dissertation pdf file [here](https://earobinson95.github.io/EmilyARobinson-UNL-dissertation/thesis.pdf).
+ View Emily A. Robinson (creator) dissertation bookdown code on the GitHub repository [here](https://github.com/earobinson95/EmilyARobinson-UNL-dissertation)

*If there are any adjustments or issues, you can contact me via my [contact form.](https://www.emilyarobinson.com/#contact)*
