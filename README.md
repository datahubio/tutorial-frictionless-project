# Tutorial: Publishing a Frictionless Dataset on Datahub

In this tutorial we are going to cover how you can publish a Frictionless Dataset with a view on DataHub, so that you can transform a repo like [this one](https://github.com/datahubio/tutorial-frictionless-project-demo), with a folder structure similar to:

```bash=
datapackage.json
README.md
data/
  vix-daily.csv
  ...
```

Into [this](https://demo.datahub.io/@datahubio/tutorial-frictionless-project-demo):

![](https://i.imgur.com/J0acu3H.png)


A Frictionless dataset, in this case, is a Data Package, as specified on the Frictionless specifications. This means that we are going to create a dataset using the battle-tested Data Package specification, push it to GitHub and render an awesome presentation for it using Datahub. You can read more about the specification [here](https://specs.frictionlessdata.io/guides/data-package/).

Without further ado, let's start building the Datahub project.

## Create a GitHub repo

In order to render the Frictionless Dataset on Datahub, we first have to create a GitHub repo for it. You can give it the name you want, just make sure that the repo is public and that the main branch is named "main".

## Push a `datapackage.json` and a data file to it

Create a `datapackage.json` file in your repo. A very basic `datapackage.json` file has the following structure:

```json
{
  "name": "a-unique-human-readable-and-url-usable-identifier",
  "title": "A nice title",
  "licenses" : [ ... ],
  "sources" : [...],
  "resources": [{...}]
}

```

In this tutorial, we are going to create a simpler version of the [@datasets/finance-vix](https://github.com/datasets/finance-vix/) dataset, so you can add the following content to your `datapackage.json` file:

```json
{
  "name": "finance-vix",
  "title": "VIX - CBOE Volatility Index",
  "profile": "data-package",
  "licenses": [
    {
      "id": "odc-pddl",
      "name": "open_data_commons_public_domain_dedication_and_license_v1.0",
      "path": "http://opendatacommons.org/licenses/pddl/",
      "title": "Open Data Commons Public Domain Dedication and License v1.0"
    }
  ],
  "resources": [
    {
      "dpp:streaming": true,
      "encoding": "utf-8",
      "format": "csv",
      "mediatype": "text/csv",
      "name": "vix-daily",
      "path": "data/vix-daily.csv",
      "profile": "tabular-data-resource",
      "schema": {
        "fields": [
          {
            "format": "any",
            "name": "Date",
            "type": "date"
          },
          {
            "format": "default",
            "name": "VIX Open",
            "type": "number"
          },
          {
            "format": "default",
            "name": "VIX High",
            "type": "number"
          },
          {
            "format": "default",
            "name": "VIX Low",
            "type": "number"
          },
          {
            "format": "default",
            "name": "VIX Close",
            "type": "number"
          }
        ],
        "missingValues": [
          ""
        ]
      },
      "title": "VIX Daily"
    }
  ],
  "sources": [
    {
      "name": "CBOE VIX Page",
      "path": "http://www.cboe.com/micro/vix/historical.aspx",
      "title": "CBOE VIX Page"
    }
  ],
  "views": [
    {
      "name": "graph",
      "spec": {
        "group": "Date",
        "series": [
          "VIX Close"
        ],
        "type": "line"
      },
      "specType": "simple",
      "title": "VIX - CBOE Volatility Index"
    }
  ]
}
```

Note the "views" property in the above file. This determines what gets rendered on the visualizations section on DataHub. You can read more about Frictionless Views [here](https://specs.frictionlessdata.io/views/#specification). Currently, DataHub supports only line charts with the "simple" specType, but you can add other visualizations to the document on the README section using the LineChart, Vega, VegaLite and Table components.

Also, note that the `group` and series properties are pointing to fields on the resource specified above. You can change change that to change how the chart behaves.

Since we are specifying a resource in the `datapackage.json` file, we also have to add the actual resource file to the repo. Create a `data` folder and inside it create a `vix-daily.csv` file with the following content:

```csv
Date,VIX Open,VIX High,VIX Low,VIX Close
2018-09-10,15.09,15.20,13.93,14.16
2018-09-11,13.96,14.92,13.21,13.22
2018-09-12,13.07,13.86,12.91,13.14
2018-09-13,12.91,12.91,12.30,12.37
2018-09-14,12.13,13.15,11.93,12.07
2018-09-17,12.72,13.75,12.32,13.68
2018-09-18,13.48,13.48,12.56,12.79
2018-09-19,12.61,12.77,11.66,11.75
2018-09-20,11.82,11.96,11.31,11.80
2018-09-21,11.76,12.03,11.10,11.68
2018-09-24,12.46,12.92,12.18,12.20
2018-09-25,12.28,12.60,11.80,12.42
2018-09-26,12.21,13.13,11.55,12.89
2018-09-27,12.77,13.00,11.94,12.41
2018-09-28,12.59,13.22,12.09,12.12
2018-10-01,11.99,12.40,11.57,12.00
2018-10-02,12.47,12.69,11.61,12.05
2018-10-03,11.66,12.14,11.34,11.61
2018-10-04,12.84,15.84,12.42,14.22
2018-10-05,14.29,17.36,11.72,14.82
2018-10-08,16.05,18.38,15.69,15.69
2018-10-09,16.12,17.49,15.27,15.95
2018-10-10,16.03,22.96,15.83,22.96
2018-10-11,23.07,28.84,20.65,24.98
2018-10-12,21.63,26.80,20.88,21.31
2018-10-15,21.97,22.89,19.47,21.30
2018-10-16,20.28,20.56,17.55,17.62
2018-10-17,17.06,19.55,17.06,17.40
```

You can push those two files we created to the repo and we are good to go. Now, let's create the README.md file.

## Push a README.md file to the repo

Create a README.md and add the following content to it.

```markdown 
CBOE Volatility Index (VIX) time-series dataset including daily open, close, high and low. The CBOE Volatility Index (VIX) is a key measure of market expectations of near-term volatility conveyed by S&P 500 stock index option prices introduced in 1993.

## Daily chart

<FrictionlessView viewId={0} />

```

Note that we are now using the `FrictionlessView` data component. This data component renders views specified on the `datapackage.json` file by index. You can also make this component full width by setting the `fullWidth` property:

```jsx
<FrictionlessView fullWidth viewId={0} />
```

Push this file to the repo and move to the next step.

## Go to the URL

The repo is ready, it's time to see how it looks on DataHub. Replace `{owner}` with your GitHub organization or user id and `{project}` with the name of the repo on the following URL and access it:

demo.datahub.io/@{'{'}owner{'}'}/{'{'}project{'}'}

If everything is right, your project should look similar to this one: https://demo.datahub.io/@datahubio/tutorial-frictionless-project-demo

___

Feel free to get in contact with us and let us know what you think : ).
