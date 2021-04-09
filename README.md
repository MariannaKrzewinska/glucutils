# Glucose and β-ketones data extraction

Process of extracting data from various glucometers using [glucometerutils](https://github.com/glucometers-tech/glucometerutils) with data format description.

## How to run
```bash
> docker build -t glucutils .
> docker run -it -v /dev:/dev -v $(pwd):/workspace --privileged --rm glucutils bash

$ python3 -m venv $(pwd)/glucometerutils-venv
$ . glucometerutils-venv/bin/activate

(glucometerutils-venv) $ export DRIVER=fslibre # for other supported devices, see glucometerutils README

(glucometerutils-venv) $ pip3 install "git+https://github.com/glucometers-tech/glucometerutils.git#egg=glucometerutils[${DRIVER}]" hidapi

(glucometerutils-venv) $ glucometer --driver ${DRIVER} info > info.txt # get device info and save to info.txt

(glucometerutils-venv) $ glucometer --driver ${DRIVER} dump --with-ketone > data.csv # get data entries and save to data.csv
```

### `data.csv`
```csv
"2020-03-07 22:08:55","88.00","","blood sample","(Blood)"
"2020-03-09 17:13:43","1.02","","blood sample","(Ketone)"
"2020-03-10 17:38:28","98.00","","blood sample","(Blood)"
"2020-04-05 10:53:00","","","time","2021-04-05 9:53:09"
```

### Data format

```json
{
  "type": "array",
  "name": "data",
  "description": "data entries",
  "items": {
    "type": "array",
    "items": [
      {
        "type": "string",
        "name": "timestamp",
        "pattern": "\\d{4}\\-(0?[1-9]|1[012])\\-(0?[1-9]|[12][0-9]|3[01]) ([01][0-9]|2[0-3]):([0-5][0-9]):([0-5][0-9])"
      },
      {
        "type": "string",
        "name": "value [mmol/L] or [mg/dL]",
        "description": "glucose or β-ketones measurement value",
        "pattern": "[0-9]{1,3}.[0-9]{1,2}"
      },
      {
        "type": "string",
        "name": "meal value",
        "description": "unavailable in Freestyle Libre"
      },
      {
        "type": "string",
        "name": "measurement method",
        "enum": ["blood sample", "CGM", "time"]
      },
      {
        "type": "array",
        "name": "comment",
        "items": {
          "type": "string",
          "enum": [
            "(Blood)",
            "(Ketone)",
            "(Scan)",
            "Sport",
            "Medication",
            "Food",
            "Long-acting insulin",
            "Rapid-acting insulin"
            // "custom",
            // "Food ([0-9]+ g)",
            // "Long-acting insulin ([0-9]{1,2}.[0-9]{1})",
            // "Rapid-acting insulin ([0-9]{1,2}.[0-9]{1})",
            // "\\d{4}\\-(0?[1-9]|1[012])\\-(0?[1-9]|[12][0-9]|3[01]) ([01][0-9]|2[0-3]):([0-5][0-9]):([0-5][0-9])"
          ]
        }
      }
    ]
  }
}
```
