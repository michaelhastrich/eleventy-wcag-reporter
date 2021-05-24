# WCAG Reporter

This repository lets you create WCAG EM Reports using 11ty.

* Write issues as Markdown files
* Create reports in English or Dutch 
* Automatically output a score card in the report 

## Status of the project

This is a side project, it comes with no warranty. 

## Set up

1. Install Eleventy globally with `npm install -g @11ty/eleventy`

## Create a new report

### Use the shell script

You can run `./add_report.sh`, which will ask for a name and duplicate the example folder with that name.

### Manually 

1. Copy the demo folder and give it a unique name (for instance: “mysite”)
2. Add “mysite” into `reports.json`. This will tell 11ty to group its issues.
3. Provide meta data in `mysite/index.njk`

## Add issues 

1. Add issues as Markdown files the report's folder
2. Add metadata as YAML frontmatter

### Metadata

Issues accept some meta data to help create the report:

| Key     | Example | Description |
|------|----------|------|
| sc | 2.4.7 | The Success Criterion the issue falls under, numbers only, separted by dots or `none` to render as tip |
| severity | Low, Medium, High | Group issues by severity (these are just strings, use what works for you or your client) |
| difficulty | Low, Medium, High | Group issues by difficulty |
| title | Focus style missing | Name for the issue |
| sample | homepage |  Refer issue back to sample ID, or `all` if not related to specific example |

## How to view reports

1. Run `eleventy --serve` to start a server, this should show you links to all your reports

## Changing stuff

This tool is meant to help generate WCAG EM reports effectively. Nothing is set in stone, you can change it to your liking.

This section explains where things live.

| Component            | Explanation  | Where to change  |
|----------------|----------|---------------|
| About this report    | Explains to reader how the report was created, how to interpret, etc | `src/_shared-content/[language]/about-this-report.md` | 
| Executive summary |  High level summary of results  | `summary.md` in the root of the report's folder |
| Report template | Blueprint for the report, decides order, etc | `src/_layouts/report.njk` |
| Surrounding HTML | `head`, etc | `src/_layouts/base.njk` |
| Translations | Strings in multiple languages, used in the templates | `src/_data/translations.json` |
| Styles for the report itself | | `src/_assets/report.css` |
| Scope, baseline, evaluators etc | Meta information displayed in the report | `src/reports/[your-report]/index.njk` |
| Language | Language for the report, also used for pulling in the correct translations and explanations | `src/reports/[your-report]/index.njk` |

## Filters and shortcodes

There are a couple of reporting-related filters available.

### Filter: link to success criterion

Creates a link to a success criterion in the [WCAG QuickRef](https://www.w3.org/WAI/WCAG21/quickref/?versions=2.1), based on a WCAG SC number.

The mapping from number (e.g. `1.1.1`) to slug (e.g. `non-text-content`) is done in `.src/data/sc_to_slug.json`.

For instance: 

```
{% 1.1.1 | sc_uri(targetWcagVersion, language) %}
```

where: 

* `language` is the page's language (required) 
* `targetWcagVersion` is the WCAG version you're evaluating against, for example `2.1` (required)


displays: https://www.w3.org/WAI/WCAG21/quickref/?versions=2.1&showtechniques=248#non-text-content

This can be used in a link, like so:

```
<a href="{% 1.1.1 | sc_uri(targetWcagVersion, language) %}">1.1.1</a>
```

And this content can be variable. If the SC number is in a variable called `sc`, this is how to link:

```
<a href="{% sc | sc_uri(targetWcagVersion, language) %}">{{ sc }}</a>
```


### Shortcode: results table

A table of results can be generated like this: 

``` 
{% sc_table issueList, language, targetLevel %}
```

where:

* `issueList` is an object with all issues for this report (assumes each issue has `sc` in front matter with a number, like `1.1.1`) (required)
* `language` is the page's language (required) 
* `targetLevel` is the evaluation target, one of these options: `A`, `AA` or `AAA` (required)
* `targetWcagVersion` is the WCAG version you're evaluating against, for example: `2.1` (required)

### Shortcode: sample image

With the `sample_image` shortcode, you can output the URL of a screenshot for a sample.

``` 
{% sample_image id, reportName %}
```

You'll need to add the id of the sample (e.g. `sample-1`) and the folder name of the report (e.g. `example`). 

This will then output either `images/sample-1.png` (if it exists) or the URL to a default image.

The default can be changed by updating `.src/reports/example/images/default-screenshot.png`.
