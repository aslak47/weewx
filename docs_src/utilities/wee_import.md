# wee_import

Some WeeWX users will have historical data from another source (e.g., other
weather station software or a manually compiled file) which they wish to
import into WeeWX. Such data can, depending upon the source, be imported
using the `wee_import` utility. This section details the use of the
`wee_import` utility.

The `wee_import` utility supports importing observational data from the
following sources:

* a single Comma Separated Values (CSV) format file
* the historical observations of a Weather Underground personal weather station
* one or more Cumulus monthly log files
* one or more Weather Display monthly log files

Before starting, it's worth running the utility with the `--help` flag to see
how `wee_import` is used:

```
wee_import --help
```
```
Usage: wee_import --help
       wee_import --version
       wee_import --import-config=IMPORT_CONFIG_FILE
            [--config=CONFIG_FILE]
            [--date=YYYY-mm-dd | --from=YYYY-mm-dd[THH:MM] --to=YYYY-mm-dd[THH:MM]]
            [--dry-run]
            [--verbose]
            [--no-prompt]
            [--suppress-warnings]


Import observation data into a WeeWX archive.

Options:
  -h, --help            show this help message and exit
  --config=CONFIG_FILE  Use configuration file CONFIG_FILE.
  --import-config=IMPORT_CONFIG_FILE
                        Use import configuration file IMPORT_CONFIG_FILE.
  --dry-run             Print what would happen but do not do it.
  --date=YYYY-mm-dd     Import data for this date. Format is YYYY-mm-dd.
  --from=YYYY-mm-dd[THH:MM]
                        Import data starting at this date or date-time. Format
                        is YYYY-mm-dd[THH:MM].
  --to=YYYY-mm-dd[THH:MM]
                        Import data up until this date or date-time. Format is
                        YYYY-mm-dd[THH:MM].
  --verbose             Print and log useful extra output.
  --no-prompt           Do not prompt. Accept relevant defaults and all y/n
                        prompts.
  --suppress-warnings   Suppress warnings to stdout. Warnings are still
                        logged.
  --version             Display wee_import version number.

wee_import will import data from an external source into a WeeWX
archive. Daily summaries are updated as each archive record is
imported so there should be no need to separately rebuild the daily
summaries using the wee_database utility.
```

## Options

### `--config=FILENAME`


The utility is pretty good about guessing where the configuration file is,
but if you have an unusual installation or multiple stations, you may have to
tell it explicitly.

```
wee_import --config=/this/directory/weewx.conf --import-config=/directory/import.conf
```

### `--import-config=FILENAME`

`wee_import` uses a secondary configuration file, known as the import
configuration file, to store various import parameters. The `--import-config`
option is mandatory for all imports. Example import configuration files for
each type of import supported by `wee_import` are provided in the
`util/import` directory. These example files are best used by making a copy of
into a working directory and then modifying the copy to suit your needs. The
`--import-config` option is used as follows:

```
wee_import --import-config=/directory/import.conf
```

### `--dry-run`

The `--dry-run` option will cause the import to proceed but no actual data will
be saved to the database. This is a useful option to use when first importing
data.

```
wee_import --import-config=/directory/import.conf --dry-run
```

### `--date=YYYY-mm-dd`

Records from a single date can be imported by use of the `--date` option. The `--date` option accepts strings of the format `YYYY-mm-dd`. Whilst the use of the `--date` option will limit the imported data to that of a single date, the default action if the `--date` option (and the `--from` and `--to` options) is omitted may vary depending on the source. The operation of the `--date` option is summarised in the following table:

<table class="no_indent">
  <caption>Option <span class="code">--date</span></caption>
  <tbody>
    <tr class="first_row">
      <td>option</td>
      <td>Records imported for a CSV, Cumulus, Weather Display or WeatherCat import</td>
      <td>Records imported for a Weather Underground import</td>
    </tr>
    <tr>
      <td class="code first_col">omitted<br>(i.e., the default)</td>
      <td>All available records</td>
      <td>Todays records only</td>
    </tr>
    <tr>
      <td class="code first_col">--date=2015-12-22</td>
      <td>All records from 2015-12-22 00:00 (exclusive) to 2015-12-23 00:00 (inclusive)</td>
      <td>All records from 2015-12-22 00:00 (exclusive) to 2015-12-23 00:00 (inclusive)</td>
    </tr>
  </tbody>
</table>

!!! Note
    If the `--date`, `--from` and `--to` options are omitted the default is to
    import today's records only when importing from Weather Underground or to
    import all available records when importing from any other source.

!!! Note
    WeeWX considers an archive record to represent an aggregation of data over
    the archive interval preceding the archive record's timestamp. For this
    reason imports which are to be limited to a given date with the `--date`
    option will only import records timestamped after midnight at the start
    of the day concerned and up to and including midnight at the end of the
    day concerned.

### `--from` and `--to`

Whilst the `--date` option allows imported data to be limited to a single date,
the `--from` and `--to` options allow finer control by importing only the
records that fall within the date or date-time range specified by the `--from`
and `--to` options. The `--from` option determines the earliest (inclusive),
and the `--to` option determines the latest (exclusive), date or date-time of
the records being imported. The `--from` and `--to` options accept a string of
the format `YYYY-mm-dd[THH:MM]`. The T literal is mandatory if specifying a
date-time.

!!! Note
    The `--from` and `--to` options must be used as a pair, they cannot be
    used individually or in conjunction with the `--date`option.

The operation of the `--from` and `--to` options is summarised in the following
table:

<table class="no_indent">
  <caption>Options <span class="code">--from</span> and <span class="code">--to</span></caption>
  <tbody>
    <tr class="first_row">
      <td colspan='2'>options</td>
      <td>Records imported for a CSV, Cumulus, Weather Display or WeatherCat import</td>
      <td>Records imported for a Weather Underground import</td>
    </tr>
    <tr>
      <td class="code first_col">omitted<br>(i.e., the default)</td>
      <td class="code first_col">omitted<br>(i.e., the default)</td>
      <td>All available records</td>
      <td>Todays records only</td>
    </tr>
    <tr>
      <td class="code first_col">--from=2015-12-22</td>
      <td class="code first_col">--to=2015-12-29</td>
      <td>All records from 2015-12-22 00:00 (exclusive) to 2015-12-30 00:00 (inclusive)</td>
      <td>All records from 2015-12-22 00:00 (exclusive) to 2015-12-30 00:00 (inclusive)</td>
    </tr>
    <tr>
      <td class="code first_col">--from=2016-7-18T15:29</td>
      <td class="code first_col">--to=2016-7-25</td>
      <td>All records from 2016-7-18 15:29 (exclusive) to 2016-7-26 00:00 (inclusive)</td>
      <td>All records from 2016-7-18 15:29 (exclusive) to 2016-7-26 00:00 (inclusive)</td>
    </tr>
    <tr>
      <td class="code first_col">--from=2016-5-12</td>
      <td class="code first_col">--to=2016-7-22T22:15</td>
      <td>All records from 2016-5-12 00:00 (exclusive) to 2016-7-22 22:15 (inclusive)</td>
      <td>All records from 2016-5-12 00:00 (exclusive) to 2016-7-22 22:15 (inclusive)</td>
    </tr>
    <tr>
      <td class="code first_col">--from=2016-3-18T15:29</td>
      <td class="code first_col">--to=2016-6-20T22:00</td>
      <td>All records from 2016-3-18 15:29 (exclusive) to 2016-6-20 22:00 (inclusive)</td>
      <td>All records from 2016-3-18 15:29 (exclusive) to 2016-6-20 22:00 (inclusive)</td>
    </tr>
  </tbody>
</table>

!!! Note
    If the `--date`, `--from` and `--to` options are omitted the default is
    to import today's records only when importing from Weather Underground or
    to import all available records when importing from any other source.

!!! Note
    WeeWX considers an archive record to represent an aggregation of data over
    the archive interval preceding the archive record's timestamp. For this
    reason imports which are to be limited to a given timespan with the
    `--from` and `--to` options will only import records timestamped after
    the timestamp represented by the `--from` option and up to and including
    the timestamp represented by the `--to` option.

### `--verbose`

Inclusion of the `--verbose` option will cause additional information to be
printed during `wee_import` execution.

```
wee_import --import-config=/directory/import.conf --verbose
```

### `--no-prompt`

Inclusion of the `--no-prompt` option will run `wee_import` without prompts.
Relevant defaults will be used and all y/n prompts are automatically accepted
as 'y'. This may be useful for unattended use of `wee_import`.

```
wee_import --import-config=/directory/import.conf --no-prompt
```

!!! Warning
    Care must be taken when using the `--no-prompt` option as ignoring warnings
    during the import process can lead to unexpected results. Whilst existing
    data will be protected, the use or acceptance of an incorrect or unexpected
    parameter or default may lead to significant amounts of unwanted data being
    imported.

### `--suppress-warnings`

The `--suppress-warnings` option suppresses `wee_import` warning messages from
being displayed on the console during the import. `wee_import` may issue a
number of warnings during import. These warnings may be due to the source
containing more than one entry for a given timestamp or there being no data
found for a mapped import field. These warnings do not necessarily require
action, but they can consist of extensive output and thus make it difficult
to follow the import progress. Irrespective of whether `--suppress-warnings`
is used all warnings are sent to log.

```
wee_import --import-config=/directory/import.conf --suppress-warnings
```

## The import configuration file

`wee_import` requires a second configuration file, the import configuration
file, in addition to the standard WeeWX configuration file. The import
configuration file specifies the import type and various options associated
with each type of import. The import configuration file is specified using the
mandatory `--import-config` option. How you construct the import configuration
file is up to you; however, the recommended method is to copy one of the
example import configuration files located in the `/home/weewx/util/import` or
`/etc/weewx/import` directory as applicable, modify the configuration options
in the newly copied file to suit the import to be performed and then use this
file as the import configuration file.

Following is the definitive guide to the options available in the import
configuration file. Default values are provided for a number of options,
meaning that if they are not listed in the import configuration file at all
`wee_import` will pick sensible values. When the documentation below gives a
"default value" this is the value that will be used if the option is omitted.

#### `source`

The `source` option determines the type of import to be performed by
`wee_import`. The option is mandatory and must be set to one of the following:

* `CSV` to import from a single CSV format file.
* `WU` to import from a Weather Underground PWS history
* `Cumulus` to import from one or more Cumulus monthly log files.
* `WD` to import from one or more Weather Display monthly log files.
* `WeatherCat` to import from one or more WeatherCat monthly .cat files.

There is no default.

### [CSV]

The [CSV]` section contains the options relating to the import of
observational data from a CSV format file.

#### `file`

The file containing the CSV format data to be used as the source during the
import. Include full path and filename. There is no default.

#### `source_encoding`

The source file encoding. This parameter is optional and should only need be
used if the source file uses an encoding other than UTF-8 or an ASCII
compatible encoding. If used, the setting used should be a <a href="https://docs.python.org/3/library/codecs.html#standard-encodings">Python Standard Encoding</a>.

The default value is `utf-8-sig`.

#### `delimiter`

The character used to separate fields. Default is `,` (comma).

#### `decimal`

The character used as the decimal point in the source files. A full stop is
frequently used but it may be another character. This parameter must be
included in quotation marks. Default is `'.'`.

#### `interval`

Determines how the time interval (WeeWX archive table field `interval`)
between successive observations is derived. The interval can be derived by one
of three methods:

* The interval can be calculated as the time, rounded to the nearest minute, between the date-time of successive records. This method is suitable when the data was recorded at fixed intervals and there are NO missing records in the source data. Use of this method when there are missing records in the source data can compromise the integrity of the WeeWX statistical data. Select this method by setting `interval = derive`.

* The interval can be set to the same value as the `archive_interval` setting under `[StdArchive]` in `weewx.conf`. This setting is useful if the data was recorded at fixed intervals but there are some missing records and the fixed interval is the same as the `archive_interval` setting under `[StdArchive]` in `weewx.conf`. Select this method by setting `interval = conf`.

* The interval can be set to a fixed number of minutes. This setting is useful if the source data was recorded at fixed intervals but there are some missing records and the fixed interval is different to the `archive_interval` setting under `[StdArchive]` in `weewx.conf`. Select this method by setting `interval = x` where `x` is an integer number of minutes.

The default value is `derive`. If the CSV source data records are equally spaced in time, but some records are missing, then a better result may be achieved using `conf` or a fixed interval setting.

#### `qc`

Determines whether simple quality control checks are applied to imported data. Setting `qc = True` will result in `wee_import` applying the WeeWX `StdQC` minimum and maximum checks to any imported observations. `wee_import` quality control checks use the same configuration settings, and operate in the same manner, as the <a href="usersguide.htm#StdQC">`StdQC`</a> service. For example, for minimum/maximum quality checks, if an observation falls outside of the quality control range for that observation, then the observation will be set to `None`. In such cases you will be alerted through a short message similar to:

```
2016-01-12 10:00:00 AEST (1452556800) record value 'outTemp' 194.34 outside limits (0.0, 120.0)
```

As derived observations are calculated after the quality control check is applied, derived observations are not subject to quality control checks. Setting `qc = False` will result in `wee_import` not applying quality control checks to imported data.

The default is `True`.

#### `calc_missing`

Determines whether any missing derived observations will be calculated from the imported data. Setting `calc_missing = True` will result in `wee_import` using the WeeWX `StdWXCalculate` service to calculate any missing derived observations from the imported data. Setting `calc_missing = False` will result in WeeWX leaving any missing derived observations as `None`. The observations that `StdWXCalculate` can calculate are listed in the <a href="usersguide.htm#StdWXCalculate">[StdWXCalculate]</a> section of the <a href="usersguide.htm">User's Guide</a>.

The default is `True`.

#### `ignore_invalid_data`

Determines whether invalid data in a source field is ignored or the import aborted. If invalid data is found in a source field and `ignore_invalid_data` is `True` the corresponding WeeWX destination field is set to `None` and the import continues. If invalid data is found in a source field and `ignore_invalid_data` is `False` the import is aborted.

The default is `True`.

#### `tranche`

To speed up database operations imported records are committed to database in groups of records rather than individually. The size of the group is set by the `tranche` parameter. Increasing the `tranche` parameter may result in a slight speed increase but at the expense of increased memory usage. Decreasing the `tranche` parameter will result in less memory usage but at the expense of more frequent database access and likely increased time to import.

The default is `250` which should suit most users.

#### `UV_sensor`

WeeWX records a `None/null` for UV when no UV sensor is installed, whereas some weather station software records a value of 0 for UV index when there is no UV sensor installed. The `UV_sensor` parameter enables `wee_import` to distinguish between the case where a UV sensor is present and the UV index is 0 and the case where no UV sensor is present and UV index is 0. `UV_sensor = False` should be used when no UV sensor was used in producing the source data. `UV_sensor = False` will result in `None/null` being recorded in the WeeWX archive field `UV` irrespective of any UV observations in the source data. `UV_sensor = True` should be used when a UV sensor was used in producing the source data. `UV_sensor = True` will result in UV observations in the source data being stored in the WeeWX archive field `UV`.

The default is `True`.

#### `solar_sensor`

WeeWX records a `None/null` when no solar radiation sensor is installed, whereas some weather station software records a value of 0 for solar radiation when there is no solar radiation sensor installed. The `solar_sensor` parameter enables `wee_import` to distinguish between the case where a solar radiation sensor is present and solar radiation is 0 and the case where no solar radiation sensor is present and solar radiation is 0. `solar_sensor = False` should be used when no solar radiation sensor was used in producing the source data. `solar_sensor = False` will result in `None/null` being recorded in the WeeWX archive field `radiation` irrespective of any solar radiation observations in the source data. `solar_sensor = True` should be used when a solar radiation sensor was used in producing the source data. `solar_sensor = True` will result in solar radiation observations in the source data being stored in the WeeWX archive field `radiation`.

The default is `True`.

#### `raw_datetime_format`

WeeWX records each record with a unique unix epoch timestamp, whereas many weather station applications or web sources export observational data with a human readable date-time. This human readable date-time is interpreted according to the format set by the `raw_datetime_format` option. This option consists of <em> <a href="https://docs.python.org/2/library/datetime.html#strftime-and-strptime-behavior">Python strptime() format codes</a> </em> and literal characters to represent the date-time data being imported.

For example, if the source data uses the format 23 January 2015 15:34 then the appropriate setting for `raw_datetime_format` would be `%d %B %Y %H:%M`, 9:25:00 12/28/16 would use `%H:%M:%S %m/%d/%y`. If the source data provides a unix epoch timestamp as the date-time field then the unix epoch timestamp is used directly and the `raw_datetime_format` option is ignored.

The default is `%Y-%m-%d %H:%M:%S`.

!!! Note
    `wee_import` does not support the construction of the unique record date
    time stamp from separate date and time fields, rather the date-time
    information for each imported record must be contained in a single
    field. CSV data containing separate date and time fields may require
    further manual processing before they can be imported.

#### `rain`

The WeeWX `rain` field records rainfall that was recorded in the preceding archive period, so for a five minute archive period the `rain` field in each archive record would contain the total rainfall that fell in the previous five minutes. Many weather station applications provide a daily or yearly total. `wee_import` can derive the WeeWX `rain` field in one of two ways:

* If the imported rainfall data is a running total then `wee_import` can derive the WeeWX `rain` field from successive totals. For this method use `rain = cumulative`.

* If the imported rainfall data is a discrete value per date-time period then `rain = discrete` should be used.

!!! Note
    `wee_import` only supports cumulative rainfall data that resets on a
    midnight boundary, cumulative rainfall data that resets at some other
    time; e.g., 9am, is not supported. In such cases the rainfall data will
    need to be converted to either reset on a midnight boundary or a discrete
    value per date-time period and `rain = discrete` used. The former may be
    possible by selecting another rainfall field (if available) in the source
    data, otherwise it will require manual manipulation of the source data.

#### `wind_direction`

WeeWX records wind direction in degrees as a number from 0 to 360 inclusive (no wind direction is recorded as `None/null`), whereas some data sources may provide wind direction as number over a different range (e.g., -180 to +180) or may use a particular value when there is no wind direction (e.g., 0 may represent no wind direction and 360 may represent a northerly wind, or -9999 (or some similar clearly invalid number) to represent there being no wind direction). `wee_import` handles such variations in data by defining a range over which imported wind direction values are accepted. Any value outside of this range is treated as there being no wind direction and is recorded as `None/null`. Any value inside the range is normalised to the range 0 to 360 inclusive (e.g., -180 would be normalised to 180). The `wind_direction` option consists of two comma separated numbers of the format lower, upper where lower and upper are inclusive. The operation of the `wind_direction` option is best illustrated through the following table:

<table class="no_indent" style="width:50%">
  <caption>Option <span class="code">wind_direction</span></caption>
  <tbody>
    <tr class="first_row">
      <td><span class="code">wind_direction</span> option setting</td>
      <td>Source data wind direction value</td>
      <td>Imported wind direction value</td>
    </tr>
    <tr>
      <td class="code first_col" rowspan='7'>0, 360</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>160</td>
      <td>160</td>
    </tr>
    <tr>
      <td>360</td>
      <td>360</td>
    </tr>
    <tr>
      <td>500</td>
      <td><span class="code">None/null</span></td>
    </tr>
    <tr>
      <td>-45</td>
      <td><span class="code">None/null</span></td>
    </tr>
    <tr>
      <td>-9999</td>
      <td><span class="code">None/null</span></td>
    </tr>
    <tr>
      <td>No data</td>
      <td><span class="code">None/null</span></td>
    </tr>
    <tr>
      <td class="code first_col" rowspan='7'>-360, 360</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>160</td>
      <td>160</td>
    </tr>
    <tr>
      <td>360</td>
      <td>360</td>
    </tr>
    <tr>
      <td>500</td>
      <td><span class="code">None/null</span></td>
    </tr>
    <tr>
      <td>-45</td>
      <td>315</td>
    </tr>
    <tr>
      <td>-9999</td>
      <td><span class="code">None/null</span></td>
    </tr>
    <tr>
      <td>No data</td>
      <td><span class="code">None/null</span></td>
    </tr>
    <tr>
      <td class="code first_col" rowspan='7'>-180, 180</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>160</td>
      <td>160</td>
    </tr>
    <tr>
      <td>360</td>
      <td><span class="code">None/null</span></td>
    </tr>
    <tr>
      <td>500</td>
      <td><span class="code">None/null</span></td>
    </tr>
    <tr>
      <td>-45</td>
      <td>315</td>
    </tr>
    <tr>
      <td>-9999</td>
      <td><span class="code">None/null</span></td>
    </tr>
    <tr>
      <td>No data</td>
      <td><span class="code">None/null</span></td>
    </tr>
  </tbody>
</table>

The default is `0, 360`

#### `[[FieldMap]]`

The <span class='code'>[[FieldMap]]</span> stanza defines the mapping from the source data fields to WeeWX archive fields. The map consists of one row per field being imported using either of the following formats:

```
weewx_archive_field_name = csv_field_name, weewx_unit_name
```

or

```
weewx_archive_field_name = csv_field_name, text
```

Where:

* <span class="code">weewx_archive_field_name</span> is a field name in the in-use WeeWX archive table schema
* <span class="code">csv_field_name</span> is the name of a field from the CSV file
* <span class="code">weewx_unit_name</span> is the WeeWX unit name of the units used by <span class="code">csv_field_name</span>
* <span class="code">text</span> is the literal word text

This mapping allows <span class="code">wee_import</span> to take a source data field, do the appropriate unit conversion and store the resulting value in the appropriate WeeWX archive field. Source data text fields may be mapped to a WeeWX text archive field by using the second form of the field map entry where the literal <span class="code">text</span> is used in place of a WeeWX unit name. A mapping is not required for every WeeWX archive field (e.g., the source may not provide inside temperature so no <span class="code">inTemp</span> field mapping is required) and neither does every CSV field need to be included in a mapping (e.g., the source data field <span class="code">monthrain</span> may have no use if the source data field <span class="code">rain</span> provides the data for the WeeWX archive <span class="code">rain</span> field). Unused field mapping lines will not be used and may be omitted.

        <p class="note">
            <strong>Note</strong><br/>Importing of text data into text fields in the WeeWX archive is only supported for
            WeeWX archive fields that have been configured as text fields. Refer to the Wiki page
            <a href="https://github.com/weewx/weewx/wiki/Storing-text-in-the-database">Storing text in the database</a>
            for details.
        </p>

        <p>If the source data includes a field that contains a WeeWX unit system code (i.e. the equivalent of the
            WeeWX <span class="code">usUnits</span> field such as may be obtained from WeeWX or wview data) then
            this field can be mapped to the WeeWX <span class="code">usUnits</span> field and used to set the
            units used for all fields being imported. In such cases the the <span class="code">weewx_unit_name</span>
            portion of the imported fields in the field map is not used and can be omitted.
        </p>

        <p>For example, source CSV data with the following structure:</p>

        <pre class="tty">date_and_time,temp,humid,wind,dir,rainfall,rad,river
23 May 2018 13:00,17.4,56,3.0,45,0.0,956,340
23 May 2018 13:05,17.6,56,1.0,22.5,0.4,746,341
        </pre>

        <p>where <span class="code">temp</span> is temperature in Celsius, <span class="code">humid</span> is
            humidity in percent, <span class="code">wind</span> is wind speed in km/h, <span class="code">dir</span>
            is wind direction in degrees, <span class="code">rainfall</span> is rain in mm,
            <span class="code">rad</span> is radiation in watts per square meter and <span class="code">river</span>
            is river height in mm might use a field map as follows:</p>

        <pre class="tty">[[FieldMap]]
    dateTime    = date_and_time, unix_epoch
    outTemp     = temp, degree_C
    outHumidity = humid, percent
    windSpeed   = wind, km_per_hour
    windDir     = dir, degree_compass
    rain        = rainfall, mm
    radiation   = rad, watt_per_meter_squared
        </pre>

        <p>If the same source CSV data included a field <span class="code">unit_info</span> that contains WeeWX
            unit system data as follows:
        </p>

        <pre class="tty">date_and_time,temp,humid,wind,dir,rainfall,rad,river,unit_info
23 May 2018 13:00,17.4,56,3.0,45,0.0,956,340,1
23 May 2018 13:05,17.6,56,1.0,22.5,0.4,746,341,16
        </pre>

         <p>then a field map such as the following might be used:</p>

        <pre class="tty">[[FieldMap]]
    dateTime    = date_and_time, unix_epoch
    usUnits     = unit_info
    outTemp     = temp
    outHumidity = humid
    windSpeed   = wind
    windDir     = dir
    rain        = rainfall
    radiation   = rad
        </pre>

        <p class="note">
            <strong>Note</strong><br/>Any WeeWX archive fields that are derived (e.g., <span
            class="code">dewpoint</span>) and for which there is no field mapping may be calculated during import by use
            of the <span class="code">calc_missing</span> option in the <span class="config_section">[CSV]</span>
            section of the import configuration file.
        </p>

        <p class="note">
            <strong>Note</strong><br/>The <span class="code">dateTime</span> field map entry is a special case. Whereas
            other field map entries may use any supported WeeWX unit name, or no unit name if the
            <span class="code">usUnits</span> field is populated, the <span class="code">dateTime</span> field map entry
            must include the WeeWX unit name <span class="code">unix_epoch</span>. This is because
            <span class="code">wee_import</span> uses the
            <em><a href="#csv_raw_datetime_format">raw_datetime_format</a></em> config option to convert the supplied
            date-time field data to a Unix epoch timestamp before the field map is applied.
        </p>

        <h3 class="config_section">[WU]</h3>

        <p>The <span class="config_section">[WU]</span> section contains the options relating to the import of
            observational data from a Weather Underground PWS history.
        </p>

        <h4 class='config_option' id='wu_station_id'>station_id</h4>

        <p>The Weather Underground weather station ID of the PWS from which the historical data will be imported. There is
            no default.
        </p>

        <h4 class='config_option' id='wu_api_key'>api_key</h4>

        <p>The Weather Underground API key to be used to obtain the PWS history data. There is no default.</p>

        <p class="note">
            <strong>Note</strong><br/>The API key is a seemingly random string of 32 characters used to access the new
            (2019) Weather Underground API. PWS contributors can obtain an API key by logging onto the Weather
            Underground internet site and accessing Member Settings. 16 character API keys used with the previous Weather
            Underground API are not supported.
        </p>

        <h4 class='config_option' id='wu_interval'>interval</h4>

        <p>Determines how the time interval (WeeWX database field <span class="code">interval</span>) between successive
            observations is determined. This option is identical in operation to the CSV <em><a href="#csv_interval">interval</a></em>
            option but applies to Weather Underground imports only. As a Weather Underground PWS history sometimes has
            missing records, the use of <span class="code">interval = derive</span> may give incorrect or inconsistent
            interval values. Better results may be obtained by using <span class="code">interval = conf</span>
            if the current WeeWX installation has the same <span class="code">archive_interval</span> as the Weather
            Underground data, or by using <span class="code">interval = x</span> where <span class="code">x</span> is
            the time interval in minutes used to upload the Weather Underground data. The most appropriate setting will
            depend on the completeness and (time) accuracy of the Weather Underground data being imported.
        </p>

        <p>The default is <span class="code">derive</span>.</p>

        <h4 class='config_option' id='wu_qc'>qc</h4>

        <p>Determines whether simple quality control checks are applied to imported data. This option is identical in
            operation to the CSV <em><a href="#csv_qc">qc</a></em> option but applies to Weather Underground imports
            only. As Weather Underground imports at times contain nonsense values, particularly for fields for which no
            data was uploaded to Weather Underground by the PWS, the use of quality control checks on imported data can
            prevent these nonsense values from being imported and contaminating the WeeWX database. The default is <span
                class="code">True</span>.
        </p>

        <h4 class='config_option' id='wu_calc_missing'>calc_missing</h4>

        <p>Determines whether any missing derived observations will be calculated from the imported data. This option is
            identical in operation to the CSV <em><a href="#csv_calc_missing">calc_missing</a></em> option but applies
            to Weather Underground imports only. The default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='wu_ignore_invalid_data'>ignore_invalid_data</h4>

        <p>Determines whether invalid data in a source field is ignored or the import aborted. This option is identical
            in operation to the CSV <em><a href="#csv_ignore_invalid_data">ignore_invalid_data</a></em> option but
            applies to Weather Underground imports only. The default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='wu_tranche'>tranche</h4>

        <p>The number of records written to the WeeWX database in each transaction. This option is identical in
            operation to the CSV <em><a href="#csv_tranche">tranche</a></em> option but applies to Weather Underground
            imports only. The default is <span class="code">250</span> which should suit most users.
        </p>

        <h4 class='config_option' id='wu_wind_direction'>wind_direction</h4>

        <p>Determines the range of acceptable wind direction values in degrees. This option is identical in operation to
            the CSV <em><a href="#csv_wind_direction">wind_direction</a></em> option but applies to Weather Underground
            imports only. The default is <span class="code">0, 360</span> which should suit most users.
        </p>

        <h3 class="config_section">[Cumulus]</h3>

        <p>The <span class="config_section">[Cumulus]</span> section contains the options relating to the import of
            observational data from Cumulus monthly log files.
        </p>

        <h4 class='config_option' id='cumulus_directory'>directory</h4>

        <p>The full path to the directory containing the Cumulus monthly log files to be imported. Do not include a
            trailing /. There is no default.
        </p>

        <h4 class='config_option' id='cumulus_encoding'>source_encoding</h4>

        <p>The Cumulus monthly log file encoding. This option is identical in operation to the
            CSV <em><a href="#csv_encoding">source_encoding</a></em> option but applies to Cumulus imports only. The
            default is <span class="code">utf-8-sig</span>.
        </p>

        <h4 class='config_option' id='cumulus_interval'>interval</h4>

        <p>Determines how the time interval (WeeWX database field <span class="code">interval</span>) between successive
            observations is determined. This option is identical in operation to the CSV <em><a href="#csv_interval">interval</a></em>
            option but applies to Cumulus monthly log file imports only. As Cumulus monthly log files can, at times,
            have missing entries, the use of <span class="code">interval = derive</span> may give incorrect or
            inconsistent interval values. Better results may be obtained by using <span
                class="code">interval = conf</span> if the <span class="code">archive_interval</span> for the current
            WeeWX installation is the same as the Cumulus 'data log interval' setting used to generate the Cumulus
            monthly log files, or by using <span class="code">interval = x</span> where <span class="code">x</span> is
            the time interval in minutes used as the Cumulus 'data log interval' setting. The most appropriate setting
            will depend on the completeness and (time) accuracy of the Cumulus data being imported.
        </p>

        <p>The default is <span class="code">derive</span>.</p>

        <h4 class='config_option' id='cumulus_qc'>qc</h4>

        <p>Determines whether simple quality control checks are applied to imported data. This option is identical in
            operation to the CSV <em><a href="#csv_qc">qc</a></em> option but applies to Cumulus imports only. The
            default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='cumulus_calc_missing'>calc_missing</h4>

        <p>Determines whether any missing derived observations will be calculated from the imported data. This option is
            identical in operation to the CSV <em><a href="#csv_calc_missing">calc_missing</a></em> option but applies
            to Cumulus imports only. The default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='cumulus_separator'>separator</h4>

        <p>The character used as the date field separator in the Cumulus monthly log file. A solidus (/) is frequently
            used but it may be another character depending on the settings on the machine that produced the Cumulus
            monthly log files. This parameter must be included in quotation marks. Default is <span
                class="code">'/'</span>.
        </p>

        <h4 class='config_option' id='cumulus_delimiter'>delimiter</h4>

        <p>The character used as the field delimiter in the Cumulus monthly log file. A comma is frequently used but it
            may be another character depending on the settings on the machine that produced the Cumulus monthly log
            files. This parameter must be included in quotation marks. Default is <span class="code">','</span>.
        </p>

        <h4 class='config_option' id='cumulus_decimal'>decimal</h4>

        <p>The character used as the decimal point in the Cumulus monthly log files. A full stop is frequently used but
            it may be another character depending on the settings on the machine that produced the Cumulus monthly log
            files. This parameter must be included in quotation marks. Default is <span class="code">'.'</span>.
        </p>

        <h4 class='config_option' id='cumulus_ignore_invalid_data'>ignore_invalid_data</h4>

        <p>Determines whether invalid data in a source field is ignored or the import aborted. This option is identical
            in operation to the CSV <em><a href="#csv_ignore_invalid_data">ignore_invalid_data</a></em> option but
            applies to Cumulus monthly log file imports only. The default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='cumulus_tranche'>tranche</h4>

        <p>The number of records written to the WeeWX database in each transaction. This option is identical in
            operation to the CSV <em><a href="#csv_tranche">tranche</a></em> option but applies to Cumulus monthly log
            file imports only. The default is <span class="code">250</span> which should suit most users.
        </p>

        <h4 class='config_option' id='cumulus_UV'>UV_sensor</h4>

        <p>Enables <span class="code">wee_import</span> to distinguish between the case where a UV sensor is present and
            the UV index is 0 and the case where no UV sensor is present and UV index is 0. This option is identical in
            operation to the CSV <em><a href="#csv_UV">UV_sensor</a></em> option but applies to Cumulus monthly log file
            imports only. The default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='cumulus_solar'>solar_sensor</h4>

        <p>Enables <span class="code">wee_import</span> to distinguish between the case where a solar radiation sensor
            is present and the solar radiation is 0 and the case where no solar radiation sensor is present and solar
            radiation is 0. This option is identical in operation to the CSV <em><a
                href="#csv_solar">solar_sensor</a></em> option but applies to Cumulus monthly log file imports only. The
            default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='cumulus_units'>[[Units]]</h4>

        <p>The <span class="code">[[Units]]</span> stanza defines the units used in the Cumulus monthly log files. Units
            settings are required for <span class="code">temperature</span>, <span class="code">pressure</span>, <span
                class="code">rain</span> and <span class="code">speed</span>. The format for each setting is:
        </p>

        <pre class="tty">obs_type = weewx_unit_name</pre>

        <p>Where <span class="code">obs_type</span> is one of <span class="code">temperature</span>, <span class="code">pressure</span>,
            <span class="code">rain</span> or <span class="code">speed</span> and <span
                class="code">weewx_unit_name</span> is the WeeWX unit name of the units used by that particular <span
                class="code">obs_type</span>. As Cumulus supports a different suite of possible units only a subset of
            the available WeeWX unit names can be used for some settings.
        </p>

        <h3 class="config_section">[WD]</h3>

        <p>The <span class="config_section">[WD]</span> section contains the options relating to the import of
            observational data from Weather Display monthly log files.
        </p>

        <h4 class='config_option' id='wd_directory'>directory</h4>

        <p>The full path to the directory containing the Weather Display monthly log files to be imported. Do not include
            a trailing /. There is no default.
        </p>

        <h4 class='config_option' id='wd_logs_to_process'>logs_to_process</h4>

        <p>The Weather Display monthly log files to be processed. Weather Display uses multiple files to record each
            month of data. Which monthly log files are produced depends on the Weather Display configuration and the
            capabilities of the weather station. <span class="code">wee_import</span> supports the following Weather
            Display monthly log files:

        <ul>
            <li>MMYYYYlg.txt</li>
            <li>MMYYYYlgcsv.csv (csv format version of MMYYYYlg.txt)</li>
            <li>MMYYYYvantagelog.txt</li>
            <li>MMYYYYvantagelogcsv.csv (csv format version of MMYYYYvantagelog.txt)</li>
            <li>MMYYYYvantageextrasensorslog.csv</li>

        <p>where MM is a one or two digit month and YYYY is a four digit year</p>

        </ul>

        <p>The format for the <span class="code">logs_to_process</span> setting is:
        </p>

        <pre class="tty">logs_to_process = [lg.txt, | logcsv.csv, | vantagelog.txt, | vantagelogcsv.csv, | vantageextrasensorslog.csv]</pre>

        <p class="note">
            <strong>Note</strong><br/>The leading MMYYYY is omitted when listing the monthly log files to be processed
            using the <span class="code">logs_to_process</span> setting. Inclusion of the leading MMYYYY will cause the
            import to fail.
        </p>

        <p class="note">
            <strong>Note</strong><br/>The MMYYYYlgcsv.csv and MMYYYYvantagelogcsv.csv log files are CSV versions of
            MMYYYYlg.txt and MMYYYYvantagelog.txt respectively. Either the .txt or .csv version of these files should be
            used but not both.
        </p>

        <p>The monthly log files selected for processing should be chosen carefully as the selected log files will
            determine the Weather Display data fields available for import. <span class="code">wee_import</span> is able
            to import the following data from the indicated monthly log files:
        </p>

            <ul>
                <li>MMYYYYlg.txt/MMYYlgcsv.csv:</li>

                    <ul>
                        <li><span class="code">average wind speed</span></li>
                        <li><span class="code">barometer</span></li>
                        <li><span class="code">date and time</span></li>
                        <li><span class="code">dew point</span></li>
                        <li><span class="code">heat index</span></li>
                        <li><span class="code">outside humidity</span></li>
                        <li><span class="code">outside temperature</span></li>
                        <li><span class="code">rain fall</span></li>
                        <li><span class="code">wind direction</span></li>
                        <li><span class="code">wind gust speed</span></li>
                    </ul>

                <li>MMYYYYvantagelog.txt/MMYYYYvantagelogcsv.csv:</li>

                    <ul>
                        <li><span class="code">date and time</span></li>
                        <li><span class="code">soil moisture</span></li>
                        <li><span class="code">soil temperature</span></li>
                        <li><span class="code">solar radiation</span></li>
                        <li><span class="code">UV index</span></li>
                    </ul>

                <li>MMYYYYvantageextrasensorslog.csv:</li>

                    <ul>
                        <li><span class="code">date and time</span></li>
                        <li><span class="code">extra humidity 1</span></li>
                        <li><span class="code">extra humidity 2</span></li>
                        <li><span class="code">extra humidity 3</span></li>
                        <li><span class="code">extra humidity 4</span></li>
                        <li><span class="code">extra humidity 5</span></li>
                        <li><span class="code">extra humidity 6</span></li>
                        <li><span class="code">extra temperature 1</span></li>
                        <li><span class="code">extra temperature 2</span></li>
                        <li><span class="code">extra temperature 3</span></li>
                        <li><span class="code">extra temperature 4</span></li>
                        <li><span class="code">extra temperature 5</span></li>
                        <li><span class="code">extra temperature 6</span></li>
                    </ul>
            </ul>

        <p class="note">
            <strong>Note</strong><br/>Whilst the above log files may contain the indicated data the data may only be
            imported subject to a suitable field map and in-use WeeWX archive table schema (refer
            to the <a href="#wd_fieldmap">[[FieldMap]] option</a>).
        </p>

        <p>The default is <span class="code">lg.txt, vantagelog.txt, vantageextrasensorslog.csv</span>.
        </p>

        <h4 class='config_option' id='wd_encoding'>source_encoding</h4>

        <p>The Weather Display monthly log file encoding. This option is identical in operation to the
            CSV <em><a href="#csv_encoding">source_encoding</a></em> option but applies to Weather Display imports only. The
            default is <span class="code">utf-8-sig</span>.
        </p>

        <h4 class='config_option' id='wd_interval'>interval</h4>

        <p>Determines how the time interval (WeeWX database field <span class="code">interval</span>) between successive
            observations is determined. This option is identical in operation to the CSV <em><a href="#csv_interval">interval</a></em>
            option but applies to Weather Display monthly log file imports only. As Weather Display log files nominally
            have entries at one minute intervals the recommended approach is to set <span class="code">interval = 1</span>.
            As Weather Display monthly log files can, at times, have missing entries, the use of
            <span class="code">interval = derive</span> may give incorrect or inconsistent interval values. If the
            <span class="code">archive_interval</span> for the current WeeWX installation is 1 minute
            <span class="code">interval = conf</span> may be used. In most cases the most appropriate setting will be
            <span class="code">interval = 1</span>.
        </p>

        <p>The default is <span class="code">1</span>.</p>

        <h4 class='config_option' id='wd_qc'>qc</h4>

        <p>Determines whether simple quality control checks are applied to imported data. This option is identical in
            operation to the CSV <em><a href="#csv_qc">qc</a></em> option but applies to Weather Display imports only.
            The default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='wd_calc_missing'>calc_missing</h4>

        <p>Determines whether any missing derived observations will be calculated from the imported data. This option is
            identical in operation to the CSV <em><a href="#csv_calc_missing">calc_missing</a></em> option but applies
            to Weather Display imports only. The default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='wd_txt_delimiter'>txt_delimiter</h4>

        <p>The character used as the field delimiter in Weather Display text format monthly log files (.txt files).
            A space is normally used but another character may be used if necessary. This parameter must be included in
            quotation marks. Default is <span class="code">' '</span>.
        </p>

        <h4 class='config_option' id='wd_csv_delimiter'>csv_delimiter</h4>

        <p>The character used as the field delimiter in Weather Display csv format monthly log files (.csv files). A
            comma is normally used but another character may be used if necessary. This parameter must be included in
            quotation marks. Default is <span class="code">','</span>.
        </p>

        <h4 class='config_option' id='wd_decimal'>decimal</h4>

        <p>The character used as the decimal point in the Weather Display monthly log files. A full stop is frequently
            used but another character may be used if necessary. This parameter must be included in quotation marks.
            Default is <span class="code">'.'</span>.
        </p>

        <h4 class='config_option' id='wd_ignore_missing_log'>ignore_Missing_log</h4>

        <p>Determines whether missing log files are to be ignored or the import aborted. Weather Display log files are
            complete in themselves and a missing log file will have no effect other than there will be no imported data
            for the period covered by the missing log file. The default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='wd_ignore_invalid_data'>ignore_invalid_data</h4>

        <p>Determines whether invalid data in a source field is ignored or the import aborted. This option is identical
            in operation to the CSV <em><a href="#csv_ignore_invalid_data">ignore_invalid_data</a></em> option but
            applies to Weather Display monthly log file imports only. The default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='wd_tranche'>tranche</h4>

        <p>The number of records written to the WeeWX database in each transaction. This option is identical in
            operation to the CSV <em><a href="#csv_tranche">tranche</a></em> option but applies to Weather Display monthly
            log file imports only. The default is <span class="code">250</span> which should suit most users.
        </p>

        <h4 class='config_option' id='wd_UV'>UV_sensor</h4>

        <p>Enables <span class="code">wee_import</span> to distinguish between the case where a UV sensor is present and
            the UV index is 0 and the case where no UV sensor is present and UV index is 0. This option is identical in
            operation to the CSV <em><a href="#csv_UV">UV_sensor</a></em> option but applies to Weather Display monthly
            log file imports only. The default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='wd_solar'>solar_sensor</h4>

        <p>Enables <span class="code">wee_import</span> to distinguish between the case where a solar radiation sensor
            is present and the solar radiation is 0 and the case where no solar radiation sensor is present and solar
            radiation is 0. This option is identical in operation to the CSV
            <em><a href="#csv_solar">solar_sensor</a></em> option but applies to Weather Display monthly log file imports
            only. The default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='wd_ignore_extreme_temp_hum'>ignore_extreme_temp_hum</h4>

        <p>Determines whether extreme temperature and humidity values are ignored. Weather Display log files record the
            value 255 for temperature and humidity fields if no corresponding sensor is present.
            Setting <span class="code">ignore_extreme_temp_hum = True</span> will cause temperature and humidity values
            of 255 to be ignored. Setting <span class="code">ignore_extreme_temp_hum = False</span> will cause
            temperature and humidity values of 255 to be treated as valid data to be imported. The default
            is <span class="code">True</span>.
        </p>

        <p class="note">
            <strong>Note</strong><br/>Setting <span class="code">ignore_extreme_temp_hum = False</span> will cause
            temperature and humidity values of 255 to be imported; however, these values may be rejected by the simple
            quality control checks implemented if <span class="code">qc = True</span> is used.
        </p>

        <h4 class='config_option' id='wd_units'>[[Units]]</h4>

        <p>The <span class="code">[[Units]]</span> stanza defines the units used in the Weather Display monthly log files.
            Weather Display monthly log files normally use Metric or US customary units depending on the <i>Log File</i>
            setting under <i>Units</i> on the <i>Units/Wind Chill</i> tab of the Weather Display <i>Universal Setup</i>.
            In such cases the <span class="code">units</span> configuration option may be set to <span class="code">Metric</span>
            or <span class="code">US</span> to select either Metric or US customary units. There is no default.
        </p>

        <p>
            It is also possible to individually specify the log file units used for <span class="code">temperature</span>,
            <span class="code">pressure</span>, <span class="code">rain</span> and <span class="code">speed</span>. The
            format for each setting is:
        </p>

        <pre class="tty">obs_type = weewx_unit_name</pre>

        <p>Where <span class="code">obs_type</span> is one of <span class="code">temperature</span>,
            <span class="code">pressure</span>, <span class="code">rain</span> or <span class="code">speed</span> and
            <span class="code">weewx_unit_name</span> is the WeeWX unit name of the units used by that particular
            <span class="code">obs_type</span>. As Weather Display supports a different suite of possible units only a
            subset of the available WeeWX unit names can be used for some settings.
        </p>

        <p>
            The preferred method for defining the Weather Display log file units is through the use of the
            <span class="code">units</span> configuration option. When defining the import log file units either the
            <span class="code">units</span> configuration option should be used or the individual
            <span class="code">temperature</span>, <span class="code">pressure</span>, <span class="code">rain</span>
            and <span class="code">speed</span> units defined but not both. If both the <span class="code">units</span>
            configuration option is defined as well as the individual <span class="code">temperature</span>,
            <span class="code">pressure</span>, <span class="code">rain</span> and <span class="code">speed</span> units
            defined then the <span class="code">units</span> configuration option takes precedence and all other units
            settings are ignored.
        </p>

        <h4 class='config_option' id='wd_fieldmap'>[[FieldMap]]</h4>

        <p>The <span class='code'>[[FieldMap]]</span> stanza defines the mapping from the Weather Display monthly log
            data fields to WeeWX archive fields. By default imported Weather Display data is mapped to the corresponding
            WeeWX archive fields using a default field map. The default field map will likely suit most users; however,
            depending on the station capabilities and the in-use WeeWX database schema, a custom field map may be
            required if Weather Display monthly logs contain data from additional sensors that cannot be stored in the
            WeeWX archive using the default field map. A custom field map also makes it possible to limit the Weather
            Display monthly log data fields that are imported into WeeWX.
        </p>

        <p>
            The field map consists of one row per field using the format:
        </p>

        <pre class="tty">weewx_archive_field_name = weather_display_field_name
</pre>

        <p>Where <span class="code">weewx_archive_field_name</span> is a field name in the in-use WeeWX archive table
            schema and <span class="code">weather_display_field_name</span> is a Weather Display import field name. The
            available Weather Display import field names are listed in the table below.
        </p>

        <table class="indent" style="width:50%">
            <caption>Available Weather Display import fields</caption>
            <tbody>
            <tr class="first_row">
                <td>Field name</td>
                <td>Description</td>
            </tr>
            <tr>
                <td class="first_col code">barometer</td>
                <td>barometric pressure</td>
            </tr>
            <tr>
                <td class="first_col code">dewpoint</td>
                <td>dew point</td>
            </tr>
            <tr>
                <td class="first_col code">direction</td>
                <td>wind direction</td>
            </tr>
            <tr>
                <td class="first_col code">gustspeed</td>
                <td>wind gust speed</td>
            </tr>
            <tr>
                <td class="first_col code">heatindex</td>
                <td>heat index</td>
            </tr>
            <tr>
                <td class="first_col code">humidity</td>
                <td>outside humidity</td>
            </tr>
            <tr>
                <td class="first_col code">hum1</td>
                <td>extra humidity 1</td>
            </tr>
            <tr>
                <td class="first_col code">hum2</td>
                <td>extra humidity 2</td>
            </tr>
            <tr>
                <td class="first_col code">hum3</td>
                <td>extra humidity 3</td>
            </tr>
            <tr>
                <td class="first_col code">hum4</td>
                <td>extra humidity 4</td>
            </tr>
            <tr>
                <td class="first_col code">hum5</td>
                <td>extra humidity 5</td>
            </tr>
            <tr>
                <td class="first_col code">hum6</td>
                <td>extra humidity 6</td>
            </tr>
            <tr>
                <td class="first_col code">radiation</td>
                <td>solar radiation</td>
            </tr>
            <tr>
                <td class="first_col code">rainlastmin</td>
                <td>rainfall in the last 1 minute</td>
            </tr>
            <tr>
                <td class="first_col code">soilmoist</td>
                <td>soil moisture</td>
            </tr>
            <tr>
                <td class="first_col code">soiltemp</td>
                <td>soil temperature</td>
            </tr>
            <tr>
                <td class="first_col code">temperature</td>
                <td>outside temperature</td>
            </tr>
            <tr>
                <td class="first_col code">temp1</td>
                <td>extra temperature 1</td>
            </tr>
            <tr>
                <td class="first_col code">temp2</td>
                <td>extra temperature 2</td>
            </tr>
            <tr>
                <td class="first_col code">temp3</td>
                <td>extra temperature 3</td>
            </tr>
            <tr>
                <td class="first_col code">temp4</td>
                <td>extra temperature 4</td>
            </tr>
            <tr>
                <td class="first_col code">temp5</td>
                <td>extra temperature 5</td>
            </tr>
            <tr>
                <td class="first_col code">temp6</td>
                <td>extra temperature 6</td>
            </tr>
            <tr>
                <td class="first_col code">uv</td>
                <td>UV index</td>
            </tr>
            <tr>
                <td class="first_col code">windspeed</td>
                <td>average wind speed</td>
            </tr>
            </tbody>
        </table>

        <p>A mapping is not required for every WeeWX archive field (e.g., the Weather Display monthly logs may not
            provide inside temperature so no <span class="code">inTemp</span> field mapping is required) and neither
            does every Weather Display monthly log field need to be included in a mapping (e.g., the Weather Display
            monthly log field <span class="code">soiltemp</span> may have no data as the station has no soil temperature
            probe).
        </p>

        <p class="note">
            <strong>Note</strong><br/>Any WeeWX archive fields that are derived
            (e.g., <span class="code">dewpoint</span>) and for which there is no field mapping may be calculated during
            import by use of the <span class="code">calc_missing</span> option in
            the <span class="config_section">[WD]</span> section of the import configuration file.
        </p>

        <p>The example Weather Display import configuration file located in the <span class="code">/home/weewx/util/import</span>
            or the <span class="code">/etc/weewx/import</span> directory contains an example field map in the import
            configuration file comments. There is no default.
        </p>

        <h3 class="config_section">[WeatherCat]</h3>

        <p>The <span class="config_section">[WeatherCat]</span> section contains the options relating to the import of
            observational data from WeatherCat monthly .cat files.
        </p>

        <h4 class='config_option' id='wcat_directory'>directory</h4>

        <p>The full path to the directory containing the year directories that contain the WeatherCat monthly .cat files
            to be imported. Do not include a trailing /. There is no default.
        </p>

        <h4 class='config_option' id='wcat_encoding'>source_encoding</h4>

        <p>The WeatherCat monthly .cat file encoding. This option is identical in operation to the
            CSV <em><a href="#csv_encoding">source_encoding</a></em> option but applies to WeatherCat imports only. The
            default is <span class="code">utf-8-sig</span>.
        </p>

        <h4 class='config_option' id='wcat_interval'>interval</h4>

        <p>Determines how the time interval (WeeWX database field <span class="code">interval</span>) between successive
            observations is determined. This option is identical in operation to the CSV <em><a href="#csv_interval">interval</a></em>
            option but applies to WeatherCat imports only. As WeatherCat monthly .cat files can, at times, have
            missing entries, the use of <span class="code">interval = derive</span> may give incorrect or
            inconsistent interval values. Better results may be obtained by using <span
                class="code">interval = conf</span> if the <span class="code">archive_interval</span> for the current
            WeeWX installation is the same as the WeatherCat .cat file log interval, or by
            using <span class="code">interval = x</span> where <span class="code">x</span> is
            the time interval in minutes used in the WeatherCat monthly .cat file(s). The most appropriate setting
            will depend on the completeness and (time) accuracy of the WeatherCat data being imported.
        </p>

        <p>The default is <span class="code">derive</span>.</p>

        <h4 class='config_option' id='wcat_qc'>qc</h4>

        <p>Determines whether simple quality control checks are applied to imported data. This option is identical in
            operation to the CSV <em><a href="#csv_qc">qc</a></em> option but applies to WeatherCat imports only. The
            default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='wcat_calc_missing'>calc_missing</h4>

        <p>Determines whether any missing derived observations will be calculated from the imported data. This option is
            identical in operation to the CSV <em><a href="#csv_calc_missing">calc_missing</a></em> option but applies
            to WeatherCat imports only. The default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='wcat_decimal'>decimal</h4>

        <p>The character used as the decimal point in the WeatherCat monthly .cat files. This parameter must be included
            in quotation marks. Default is <span class="code">'.'</span>.
        </p>

        <h4 class='config_option' id='wcat_tranche'>tranche</h4>

        <p>The number of records written to the WeeWX database in each transaction. This option is identical in
            operation to the CSV <em><a href="#csv_tranche">tranche</a></em> option but applies to WeatherCat imports
            only. The default is <span class="code">250</span> which should suit most users.
        </p>

        <h4 class='config_option' id='wcat_UV'>UV_sensor</h4>

        <p>Enables <span class="code">wee_import</span> to distinguish between the case where a UV sensor is present and
            the UV index is 0 and the case where no UV sensor is present and UV index is 0. This option is identical in
            operation to the CSV <em><a href="#csv_UV">UV_sensor</a></em> option but applies to WeatherCat imports only.
            The default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='wcat_solar'>solar_sensor</h4>

        <p>Enables <span class="code">wee_import</span> to distinguish between the case where a solar radiation sensor
            is present and the solar radiation is 0 and the case where no solar radiation sensor is present and solar
            radiation is 0. This option is identical in operation to the
            CSV <em><a href="#csv_solar">solar_sensor</a></em> option but applies to WeatherCat imports only. The
            default is <span class="code">True</span>.
        </p>

        <h4 class='config_option' id='wcat_units'>[[Units]]</h4>

        <p>The <span class="code">[[Units]]</span> stanza defines the units used in the WeatherCat monthly .cat files.
            Unit settings are required for <span class="code">temperature</span>, <span class="code">pressure</span>, <span
                class="code">rain</span> and <span class="code">speed</span>. The format for each setting is:
        </p>

        <pre class="tty">obs_type = weewx_unit_name</pre>

        <p>Where <span class="code">obs_type</span> is one of <span class="code">temperature</span>, <span class="code">pressure</span>,
            <span class="code">rain</span> or <span class="code">speed</span> and
            <span class="code">weewx_unit_name</span> is the WeeWX unit name of the units used by that particular
            <span class="code">obs_type</span> (refer to the <a href="customizing.htm#units">Units</a> appendix in
            the Customization Guide for details of available WeeWX unit names). As WeatherCat supports a different suite
            of possible units only a subset of the available WeeWX unit names can be used for some settings.
        </p>

        <h2>Importing from CSV files</h2>

        <p class="warning">
            <strong>Warning!</strong><br/>Running WeeWX during a <span class="code">wee_import</span> session can lead to
            abnormal termination of the import. If WeeWX must remain running (e.g., so that live data is not lost) run
            the <span class="code">wee_import</span> session on another machine or to a second database and merge the
            in-use and second database once the import is complete.
        </p>

        <p><span class="code">wee_import</span> can import data from a single CSV file. The CSV source file must be
            structured as follows:
        </p>

        <ul>
            <li>The file must have a header row consisting of a comma separated list of field names. The field names can
                be any valid string as long as each field name is unique within the list. There is no requirement for
                the field names to be in any particular order as long as the same order is used for the observations on
                each row in the file. These field names will be mapped to WeeWX field names in the <span class="code">[CSV]</span>
                section of the import configuration file.
            </li>

            <li>Observation data for a given date-time must be listed on a single line with individual fields separated
                by a comma. The fields must be in the same order as the field names in the header row.
            </li>

            <li>Blank fields are represented by the use of white space or no space only between commas.
            </li>

            <li>Direction data being imported may be represented as numeric degrees or as a string representing
                the <a href="https://en.wikipedia.org/wiki/Cardinal_direction">cardinal, intercardinal and/or secondary intercardinal directions</a>.
            </li>

            <li>There must a field that represents the date-time of the observations on each line. This date-time field
                must be either a Unix epoch timestamp or any date-time format that can be represented using <em> <a
                    href="https://docs.python.org/2/library/datetime.html#strftime-and-strptime-behavior">Python
                    strptime() format codes</a></em>.
            </li>
        </ul>

        <p>A CSV file suitable for import by <span class="code">wee_import</span> may look like this:</p>

        <pre class="tty">Time,Barometer,Temp,Humidity,Windspeed,Dir,Gust,Dayrain,Radiation,Uv,Comment
28/11/2017 08:00:00,1016.9,24.6,84,1.8,113,8,0,359,3.8,"start of observations"
28/11/2017 08:05:00,1016.9,25.1,82,4.8,135,11.3,0,775,4.7,
28/11/2017 08:10:00,1016.9,25.4,80,4.4,127,11.3,0,787,5.1,"note temperature"
28/11/2017 08:15:00,1017,25.7,79,3.5,74,11.3,0,800,5.4,
28/11/2017 08:20:00,1016.9,25.9,79,1.6,95,9.7,0,774,5.5,
28/11/2017 08:25:00,1017,25.5,78,2.9,48,9.7,0,303,3.4,"forecast received"
28/11/2017 08:30:00,1017.1,25.1,80,3.1,54,9.7,0,190,3.6,</pre>

        <p>or this:</p>

        <pre class="tty">Time,Barometer,Temp,Humidity,Windspeed,Dir,Gust,Dayrain,Radiation,Uv
2/1/2017 06:20:00,1006.4,4.8,48,2.8,NE,4,0,349,2.8
2/1/2017 06:25:00,1006.9,5.0,48,3.8,NNE,21.3,0,885,4.3
2/1/2017 06:30:00,1006.8,5.4,47,3.4,North,12.3,0,887,5.3
2/1/2017 06:35:00,1007,5.2,49,5.5,NNE,13.3,0,600,5.4
2/1/2017 06:40:00,1006.9,5.7,49,2.6,ESE,9.7,0,732,5.5
2/1/2017 06:45:00,1007,5.5,48,1.9,Southsoutheast,9.8,0,393,6.4
2/1/2017 06:50:00,1007.1,5.2,50,2.1,southeast,9.9,0,180,6.6</pre>

        <p class="note">
            <strong>Note</strong><br/><a href="https://en.wikipedia.org/wiki/Cardinal_direction">Cardinal, intercardinal
            and/or secondary intercardinal directions</a> may be represented by one, two or three letter abbreviations
            e.g., N, SE or SSW; by a single word e.g., North, Southwest or Southsouthwest or by hyphenated or spaced words
            e.g., North West or South-south-west. Capitalisation is ignored as are any spaces, hyphens or other white
            space. At present only English abbreviations and directions are supported.
        </p>

        <h3>Mapping data to archive fields</h3>

        <p>The WeeWX archive fields populated during a CSV import depend on the CSV-to-WeeWX field mappings specified in
            <span class="code">[[FieldMap]]</span> stanza in the import configuration file. If a valid field mapping
            exists, the WeeWX field exists in the WeeWX archive table schema and provided the mapped CSV field contains
            valid data, then the corresponding WeeWX field will populated. Note that the CSV import is the only import
            supported by <span class="code">wee_import</span> that allows any WeeWX archive field to be populated.
        </p>

        <p class="note">
            <strong>Note</strong><br/>The use of the <span class="code"><a
            href="#csv_calc_missing">calc_missing</a></span> option in the import configuration file may result in a
            number of derived fields being calculated from the imported data. If these derived fields exist in the
            in-use database schema they will be saved to the database as well.
        </p>

        <h3>Step-by-step instructions</h3>

        <p>To import observations from a CSV file:</p>

        <ol>
            <li>Ensure the source data file is in a directory accessible by the machine that will run
                <span class="code">wee_import</span>. For the purposes of the following examples the source data file
                <span class="code">data.csv</span> located in the <span class="code">/var/tmp</span> directory will be
                used.
            </li>

            <li>Make a backup of the WeeWX database in case the import should go awry.
            </li>

            <li>Create an import configuration file. In this case we will make a copy of the example CSV import
                configuration file and save it as <span class="code">csv.conf</span> in the <span
                    class="code">/var/tmp</span> directory:
                    <pre class="tty cmd">$ cp /home/weewx/util/import/csv-example.conf /var/tmp/csv.conf</pre>
            </li>

            <li>Confirm that the <span class="code"><a href="#import_config_source">source</a></span> option is set to
                CSV:
                    <pre class="tty">source = CSV</pre>
            </li>


            <li>Confirm that the following options in the <span class="code">[CSV]</span> section are set:


                <ul>
                    <li><a href="#csv_file"><strong><span class="code">file</span></strong></a>. The full path and file
                        name of the file containing the CSV formatted data to be imported.
                    </li>
                    <li><a href="#csv_delimiter"><strong><span class="code">delimiter</span></strong></a>. The single
                        character used to separate fields.
                    </li>
                    <li><a href="#csv_interval"><strong><span class="code">interval</span></strong></a>. Determines how
                        the WeeWX interval field is derived.
                    </li>
                    <li><a href="#csv_qc"><strong><span class="code">qc</span></strong></a>. Determines whether quality
                        control checks are performed on the imported data.
                    </li>
                    <li><a href="#csv_calc_missing"><strong><span class="code">calc_missing</span></strong></a>.
                        Determines whether missing derived observations will be calculated from the imported data.
                    </li>
                    <li><a href="#csv_ignore_invalid_data"><strong><span
                        class="code">ignore_invalid_data</span></strong></a>. Determines whether invalid data in a
                        source field is ignored or the import aborted.
                    </li>
                    <li><a href="#csv_tranche"><strong><span class="code">tranche</span></strong></a>. The number of
                        records written to the WeeWX database in each transaction.
                    </li>
                    <li><a href="#csv_UV"><strong><span class="code">UV_sensor</span></strong></a>. Whether a UV sensor
                        was installed when the source data was produced.
                    </li>
                    <li><a href="#csv_solar"><strong><span class="code">solar_sensor</span></strong></a>. Whether a
                        solar radiation sensor was installed when the source data was produced.
                    </li>
                    <li><a href="#csv_raw_datetime_format"><strong><span
                        class="code">raw_datetime_format</span></strong></a>. The format of the imported date time
                        field.
                    </li>
                    <li><a href="#csv_rain"><strong><span class="code">rain</span></strong></a>. Determines how the
                        WeeWX rain field is derived.
                    </li>
                    <li><a href="#csv_wind_direction"><strong><span class="code">wind_direction</span></strong></a>.
                        Determines how imported wind direction fields are interpreted.
                    </li>
                    <li><a href="#csv_fieldmap"><strong><span class="code">[[FieldMap]]</span></strong></a>. Defines the
                        mapping between imported data fields and WeeWX archive fields. Also defines the units of measure
                        for each imported field.
                    </li>
                </ul>
            </li>

            <li>When first importing data it is prudent to do a dry run import before any data are actually imported. A
                dry run import will perform all steps of the import without actually writing imported data to the WeeWX
                database. In addition, consideration should be given to any additional options such as <span
                    class="code">--date</span>.

                <p>To perform a dry run enter the following command:</p>

                <pre class="tty cmd">wee_import --import-config=/var/tmp/csv.conf --dry-run
</pre>
                <p>The output should be something like this:</p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
A CSV import from source file '/var/tmp/data.csv' has been requested.
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
This is a dry run, imported data will not be saved to archive.
Starting dry run import ...
27337 records identified for import.
Unique records processed: 27337; Last timestamp: 2018-03-03 06:00:00 AEST (1520020800)
Finished dry run import
27337 records were processed and 27337 unique records would have been imported.
</pre>

                <p>The output includes details about the data source, the destination of the imported data and some other
                    details on how the data will be processed. The import will then be performed but no data will be
                    written to the WeeWX database. Upon completion a brief summary of the records processed is provided.
                </p>
            </li>

            <li>Once the dry run results are satisfactory the data can be imported using the following command:

                <pre class="tty cmd">wee_import --import-config=/var/tmp/csv.conf</pre>

                <p>This will result in a short preamble similar to that from the dry run. At the end of the preamble
                    there will be a prompt:
                </p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
A CSV import from source file '/var/tmp/data.csv' has been requested.
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
Starting import ...
27337 records identified for import.
Proceeding will save all imported records in the WeeWX archive.
Are you sure you want to proceed (y/n)?
</pre>
            </li>

            <li>If the import parameters are acceptable enter <span class="code">y</span> to proceed with the import or
                <span class="code">n</span> to abort the import. If the import is confirmed then the source data will be
                imported, processed and saved in the WeeWX database. Information on the progress of the import will be
                displayed similar to the following:

                <pre class="tty">Unique records processed: 3250; Last timestamp: 2017-12-09 14:45:00 AEST (1512794700)
</pre>

                <p>The line commencing with <span class="code">Unique records processed</span> should update as records are
                    imported with progress information on number of records processed, number of unique records imported
                    and the date time of the latest record processed. Once the initial import is complete
                    <span class="code">wee_import</span> will, if requested, calculate any missing derived observations and
                    rebuild the daily summaries. A brief summary should be displayed similar to the following:
                </p>

                <pre class="tty">Calculating missing derived observations...
Processing record: 27337; Last record: 2018-03-03 06:00:00 AEST (1520020800)
Recalculating daily summaries...
Records processed: 27337; Last date: 2018-03-03 06:00:00 AEST (1520020800)
Finished recalculating daily summaries
Finished calculating missing derived observations
</pre>

                <p>When the import is complete a brief summary is displayed similar to the following:
                </p>

                <pre class="tty">Finished import
27337 records were processed and 27337 unique records imported in 113.91 seconds.
Those records with a timestamp already in the archive will not have been
imported. Confirm successful import in the WeeWX log file.
</pre>

            </li>
            <li>Whilst <span class="code">wee_import</span> will advise of the number of records processed and the
                number of unique records found, <span class="code">wee_import</span> does know how many, if any, of the
                imported records were successfully saved to the database. You should look carefully through the WeeWX
                log file covering the <span class="code">wee_import</span> session and take note of any records that
                were not imported. The most common reason for imported records not being saved to the database is
                because a record with that timestamp already exists in the database, in such cases something similar to
                the following will be found in the log:

                <pre class="tty">
Aug 22 14:38:28 stretch12  wee_import[1226] ERROR weewx.manager: Unable to add record 2018-09-04 04:20:00 AEST (1535998800) to database 'weewx.sdb': UNIQUE constraint failed: archive.dateTime
</pre>

                <p>In such cases you should take note of the timestamp of the record(s) concerned and make a decision
                    about whether to delete the pre-existing record and re-import the record or retain the pre-existing
                    record.
                </p>
            </li>

        </ol>

        <h2>Importing from Weather Underground</h2>

        <p class="warning">
            <strong>Warning!</strong><br/>Running WeeWX during a <span class="code">wee_import</span> session can lead to
            abnormal termination of the import. If WeeWX must remain running (e.g., so that live data is not lost) run
            the <span class="code">wee_import</span> session on another machine or to a second database and merge the
            in-use and second database once the import is complete.
        </p>

        <p><span class="code">wee_import</span> can import historical observation data for a Weather Underground PWS via
            the Weather Underground API. The Weather Underground API provides historical weather station observations
            received by Weather Underground for the PWS concerned on a day by day basis. As such, the data is analogous
            to the WeeWX archive table. When <span class="code">wee_import</span> imports data from the Weather
            Underground API each day is considered a 'period'. <span class="code">wee_import</span> processes one period
            at a time in chronological order (oldest to newest) and provides import summary data on a per period basis.
        </p>

        <h3>Mapping data to archive fields</h3>

        <p>A Weather Underground import will populate WeeWX archive fields as follows:</p>

        <ul>
            <li>Provided data exists for each field returned by the Weather Underground API, the following WeeWX archive
                fields will be directly populated by imported data:

                <ul>
                    <li><span class="code">dateTime</span></li>
                    <li><span class="code">barometer</span></li>
                    <li><span class="code">dewpoint</span></li>
                    <li><span class="code">heatindex</span></li>
                    <li><span class="code">outHumidity</span></li>
                    <li><span class="code">outTemp</span></li>
                    <li><span class="code">radiation</span></li>
                    <li><span class="code">rain</span></li>
                    <li><span class="code">rainRate</span></li>
                    <li><span class="code">UV</span></li>
                    <li><span class="code">windchill</span></li>
                    <li><span class="code">windDir</span></li>
                    <li><span class="code">windGust</span></li>
                    <li><span class="code">windSpeed</span></li>
                </ul>

                <p class="note">
                    <strong>Note</strong><br/>If an appropriate field is not returned by the Weather Underground API then
                    the corresponding WeeWX archive field will contain no data. If the API returns an appropriate field but
                    with no data, the corresponding WeeWX archive field will be set to <span class="code">None/null</span>.
                    For example, if the API response has no solar radiation field the WeeWX
                    <span class="code">radiation</span> archive field will have no data stored. However, if the API
                    response has a solar radiation field but contains no data, the WeeWX
                    <span class="code">radiation</span> archive field will be <span class="code">None/null</span>.
                </p>
            </li>

            <li>The following WeeWX archive fields will be populated from other settings or configuration options:
                <ul>
                    <li><span class="code">interval</span></li>
                    <li><span class="code">usUnits</span></li>
                </ul>
            </li>

            <li>The following WeeWX archive fields will be populated with values derived from the imported data provided
                <span class="code">calc_missing = True</span> is included in the <span class="code">[WU]</span> section
                of the import configuration file and the field exists in the in-use WeeWX archive table schema.

                <ul>
                    <li><span class="code">altimeter</span></li>
                    <li><span class="code">ET</span></li>
                    <li><span class="code">pressure</span></li>
                </ul>

                <p class="note">
                    <strong>Note</strong><br/>If <span class="code">calc_missing = False</span> is included in the <span
                    class="code">[WU]</span> section of the import configuration file being used then all of the above
                    fields will be set to <span class="code">None/null</span>. The default setting of the <span
                    class="code">calc_missing</span> option is <span class="code">True</span>
                </p>
            </li>
        </ul>

        <h3>Step-by-step instructions</h3>

        <p>To import observations from a Weather Underground PWS history:</p>

        <ol>
            <li>Obtain the weather station ID of the Weather Underground PWS from which data is to be imported. The
                station ID will be a sequence of numbers and upper case letters that is usually 11 or 12 characters in
                length. For the purposes of the following examples a weather station ID
                of <span class="code">ISTATION123</span> will be used.
            </li>

            <li>Obtain the API key to be used to access the Weather Underground API. This will be a seemingly random
                alphanumeric sequence of 32 characters. API keys are available to Weather Underground PWS contributors
                by logging on to their Weather Underground account and accessing Member Settings.
            </li>

            <li>Make a backup of the WeeWX database in case the import should go awry.
            </li>

            <li>Create an import configuration file. In this case we will make a copy of the example Weather Underground
                import configuration file and save it as <span class="code">wu.conf</span> in the <span class="code">/var/tmp</span>
                directory:

                <pre class="tty cmd">$ cp /home/weewx/util/import/wu-example.conf /var/tmp/wu.conf</pre>
            </li>
            <li>Confirm that the <span class="code"><a href="#import_config_source">source</a></span> option is set to
                WU
            <pre class="tty">source = WU</pre>
            </li>

            <li>Confirm that the following options in the <span class="code">[WU]</span> section are correctly set:

                <ul>
                    <li><a href="#wu_station_id"><strong><span class="code">station_id</span></strong></a>. The 11 or 12
                        character weather station ID of the Weather Underground PWS that will be the source of the
                        imported data.
                    </li>
                    <li><a href="#wu_api_key"><strong><span class="code">api_key</span></strong></a>. The 32 character
                        API key to be used to access the Weather Underground API.
                    </li>
                    <li><a href="#wu_interval"><strong><span class="code">interval</span></strong></a>. Determines how
                        the WeeWX interval field is derived.
                    </li>
                    <li><a href="#wu_qc"><strong><span class="code">qc</span></strong></a>. Determines whether quality
                        control checks are performed on the imported data.

                        <p class="note">
                            <strong>Note</strong><br/>As Weather Underground imports at times contain nonsense values,
                            particularly for fields for which no data were uploaded to Weather Underground by the PWS,
                            the use of quality control checks on imported data can prevent these nonsense values from
                            being imported and contaminating the WeeWX database.
                        </p>
                    </li>

                    <li><a href="#wu_calc_missing"><strong><span class="code">calc_missing</span></strong></a>.
                        Determines whether missing derived observations will be calculated from the imported data.
                    </li>
                    <li><a href="#wu_ignore_invalid_data"><strong><span class="code">ignore_invalid_data</span></strong></a>.
                        Determines whether invalid data in a source field is ignored or the import aborted.
                    </li>
                    <li><a href="#wu_tranche"><strong><span class="code">tranche</span></strong></a>. The number of
                        records written to the WeeWX database in each transaction.
                    </li>
                    <li><a href="#wu_wind_direction"><strong><span class="code">wind_direction</span></strong></a>.
                        Determines how imported wind direction fields are interpreted.
                    </li>
                </ul>
            </li>

            <li>When first importing data it is prudent to do a dry run import before any data is actually imported. A
                dry run import will perform all steps of the import without actually writing imported data to the WeeWX
                database. In addition, consideration should be given to any additional options to be used such as <span
                    class="code">--date</span>, <span class="code">--from</span> or <span class="code">--to</span>.
                <p>To perform a dry run enter the following command:</p>

                <pre class="tty cmd">wee_import --import-config=/var/tmp/wu.conf --from=2016-01-20T22:30 --to=2016-01-23T06:00 --dry-run
</pre>

                <p>
                    In this case the <span class="code">--from</span> and <span class="code">--to</span> options have
                    been used to import Weather Underground records from 10:30pm on 20 January 2016 to 6:00am on 23
                    January 2016 inclusive.
                </p>

                <p class="note">
                    <strong>Note</strong><br/>If the <span class="code">--date</span> option is omitted, or a date (not
                    date-time) range is specified using the <span class="code">--from</span> and <span
                    class="code">--to</span> options during a Weather Underground import, then one or more full days of
                    history data will be imported. This includes records timestamped from <span
                    class="code">00:00</span> (inclusive) at the start of the day up to but NOT including the <span
                    class="code">00:00</span> record at the end of the last day. As the timestamped record refers to
                    observations of the previous interval, such an import actually includes one record with observations
                    from the previous day (the <span class="code">00:00</span> record at the start of the day). Whilst
                    this will not present a problem for <span class="code">wee_import</span> as any records being
                    imported with a timestamp that already exists in the WeeWX database are ignored, you may wish to use
                    the <span class="code">--from</span> and <span class="code">--to</span> options with a suitable
                    date-time range to precisely control which records are imported.
                </p>

                <p class="note">
                    <strong>Note</strong><br/><span class="code">wee_import</span> obtains Weather Underground daily
                    history data one day at a time via a HTTP request and as such the import of large time spans of data
                    may take some time. Such imports may be best handled as a series of imports of smaller time spans.
                </p>

                <p>This will result in a short preamble with details on the data source, the destination of the imported
                    data and some other details on how the data will be processed. The import will then be performed but
                    no data will written to the WeeWX database.
                </p>
                <p>The output should be similar to:</p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
Observation history for Weather Underground station 'ISTATION123' will be imported.
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
Observations timestamped after 2016-01-20 22:30:00 AEST (1453293000) and up to and
including 2016-01-23 06:00:00 AEST (1453492800) will be imported.
This is a dry run, imported data will not be saved to archive.
Starting dry run import ...
Records covering multiple periods have been identified for import.
Period 1 ...
Unique records processed: 18; Last timestamp: 2016-01-20 23:55:00 AEST (1453298100)
Period 2 ...
Unique records processed: 284; Last timestamp: 2016-01-21 23:55:00 AEST (1453384500)
Period 3 ...
Unique records processed: 284; Last timestamp: 2016-01-22 23:55:00 AEST (1453470900)
Period 4 ...
Unique records processed: 71; Last timestamp: 2016-01-23 06:00:00 AEST (1453492800)
Finished dry run import
657 records were processed and 657 unique records would have been imported.
</pre>
                <p class="note">
                    <strong>Note</strong><br/>Any periods for which no data could be obtained will be skipped. The lack
                    of data may be due to an incorrect station ID, an incorrect date or Weather Underground API problems.
                    A short explanatory note to this effect will be displayed against the period concerned and an entry
                    included in the log.
                </p>
            </li>

            <li>Once the dry run results are satisfactory the source data can be imported using the following command:

                <pre class="tty cmd">wee_import --import-config=/var/tmp/wu.conf --from=2016-01-20T22:30 --to=2016-01-23T06:00
</pre>

                <p>This will result in a short preamble similar to that of a dry run. At the end of the preamble there
                    will be a prompt:
                </p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
Observation history for Weather Underground station 'ISTATION123' will be imported.
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
Observations timestamped after 2016-01-20 22:30:00 AEST (1453293000) and up to and
including 2016-01-23 06:00:00 AEST (1453492800) will be imported.
Starting import ...
Records covering multiple periods have been identified for import.
Period 1 ...
Proceeding will save all imported records in the WeeWX archive.
Are you sure you want to proceed (y/n)?
</pre>

                <p class="note">
                    <strong>Note</strong><br/><span class="code">wee_import</span> obtains Weather Underground data
                    one day at a time via a HTTP request and as such the import of large time spans of data may take some
                    time. Such imports may be best handled as a series of imports of smaller time spans.
                </p>
            </li>

            <li>If the import parameters are acceptable enter <span class="code">y</span> to proceed with the import or
                <span class="code">n</span> to abort the import. If the import is confirmed, the source data will be
                imported, processed and saved in the WeeWX database. Information on the progress of the import will be
                displayed similar to the following:

                <pre class="tty">Unique records processed: 18; Last timestamp: 2016-01-20 23:55:00 AEST (1453298100)
Period 2 ...
Unique records processed: 284; Last timestamp: 2016-01-21 23:55:00 AEST (1453384500)
Period 3 ...
Unique records processed: 284; Last timestamp: 2016-01-22 23:55:00 AEST (1453470900)
</pre>
                <p class="note">
                    <strong>Note</strong><br/>Any periods for which no data could be obtained will be skipped. The lack
                    of data may be due to an incorrect station ID, an incorrect date or Weather Underground API problems.
                    A short explanatory note to this effect will be displayed against the period concerned and an entry
                    included in the log.
                </p>

                <p>The line commencing with <span class="code">Unique records processed</span> should update as records are
                    imported with progress information on number of records processed, number of unique records imported
                    and the date time of the latest record processed. If the import spans multiple days then a new <span
                        class="code">Period</span> line is created for each day.
                </p>

                <p>Once the initial import is complete <span class="code">wee_import</span> will, if requested, calculate
                    any missing derived observations and rebuild the daily summaries. A brief summary should be displayed
                    similar to the following:
                </p>

                <pre class="tty">Calculating missing derived observations ...
Processing record: 204; Last record: 2016-01-22 23:55:00 AEST (1453470900)
Recalculating daily summaries...
Finished recalculating daily summaries
Finished calculating missing derived observations
</pre>

                <p>When the import is complete a brief summary is displayed similar to the following:
                </p>

                <pre class="tty">Finished import
657 records were processed and 657 unique records imported in 78.97 seconds.
Those records with a timestamp already in the archive will not have been
imported. Confirm successful import in the WeeWX log file.
</pre>

                <p class="note">
                    <strong>Note</strong><br/>The new (2019) Weather Underground API appears to have an issue when
                    obtaining historical data for the current day. The first time the API is queried the API returns all
                    historical data up to and including the most recent record. However, subsequent later API queries
                    during the same day return the same set of records rather than all records up to and including the
                    time of the latest API query. Users importing Weather Underground data that includes data from the
                    current day are advised to carefully check the WeeWX log to ensure that all expected records were
                    imported. If some records are missing from the current day try running an import for the current day
                    again using the <span class="code">--date</span> option setting. If this fails then wait until the
                    following day and perform another import for the day concerned again using the
                    <span class="code">--date</span> option setting. In all cases confirm what data has been imported by
                    referring to the WeeWX log.
                </p>
            </li>

            <li>Whilst <span class="code">wee_import</span> will advise of the number of records processed and the
                number of unique records found, <span class="code">wee_import</span> does know how many, if any, of the
                imported records were successfully saved to the database. You should look carefully through the WeeWX
                log file covering the <span class="code">wee_import</span> session and take note of any records that
                were not imported. The most common reason for imported records not being saved to the database is
                because a record with that timestamp already exists in the database, in such cases something similar to
                the following will be found in the log:

                <pre class="tty">
Aug 22 14:38:28 stretch12 weewx[863]: manager: unable to add record 2018-09-04 04:20:00 AEST (1535998800) to database 'weewx.sdb': UNIQUE constraint failed: archive.dateTime
</pre>

                <p>In such cases you should take note of the timestamp of the record(s) concerned and make a decision
                    about whether to delete the pre-existing record and re-import the record or retain the pre-existing
                    record.
                </p>

            </li>
        </ol>

        <h2>Importing from Cumulus</h2>

        <p class="warning">
            <strong>Warning!</strong><br/>Running WeeWX during a <span class="code">wee_import</span> session can lead to
            abnormal termination of the import. If WeeWX must remain running (e.g., so that live data is not lost) run
            the <span class="code">wee_import</span> session on another machine or to a second database and merge the
            in-use and second database once the import is complete.
        </p>

        <p><span class="code">wee_import</span> can import observational data from the one or more Cumulus monthly log
            files. A Cumulus monthly log file records weather station observations for a single month. These files are
            accumulated over time and can be considered analogous to the WeeWX archive table. When <span class="code">wee_import</span>
            imports data from the Cumulus monthly log files each log file is considered a 'period'. <span class="code">wee_import</span>
            processes one period at a time in chronological order (oldest to newest) and provides import summary data on
            a per period basis.
        </p>

        <h3>Mapping data to archive fields</h3>

        <p>A Cumulus monthly log file import will populate the WeeWX archive fields as follows:</p>

        <ul>
            <li>Provided data exists for each field in the Cumulus monthly logs, the following WeeWX archive fields will
                be directly populated by imported data:

                <ul>
                    <li><span class="code">dateTime</span></li>
                    <li><span class="code">barometer</span></li>
                    <li><span class="code">dewpoint</span></li>
                    <li><span class="code">heatindex</span></li>
                    <li><span class="code">inHumidity</span></li>
                    <li><span class="code">inTemp</span></li>
                    <li><span class="code">outHumidity</span></li>
                    <li><span class="code">outTemp</span></li>
                    <li><span class="code">radiation</span></li>
                    <li><span class="code">rain</span></li>
                    <li><span class="code">rainRate</span></li>
                    <li><span class="code">UV</span></li>
                    <li><span class="code">windDir</span></li>
                    <li><span class="code">windGust</span></li>
                    <li><span class="code">windSpeed</span></li>
                    <li><span class="code">windchill</span></li>
                </ul>

                <p class="note">
                    <strong>Note</strong><br/>If a field in the Cumulus monthly log file has no data then the
                    corresponding WeeWX archive field will be set to <span class="code">None/null</span>.
                </p>
            </li>

            <li>The following WeeWX archive fields will be populated from other settings or configuration options:

                <ul>
                    <li><span class="code">interval</span></li>
                    <li><span class="code">usUnits</span></li>
                </ul>
            </li>

            <li>The following WeeWX archive fields will be populated with values derived from the imported data provided
                <span class="code">calc_missing = True</span> is included in the <span class="code">[Cumulus]</span>
                section of the import configuration file being used and the field exists in the in-use WeeWX archive
                table schema.

                <ul>
                    <li><span class="code">altimeter</span></li>
                    <li><span class="code">ET</span></li>
                    <li><span class="code">pressure</span></li>
                </ul>

                <p class="note">
                    <strong>Note</strong><br/>If <span class="code">calc_missing = False</span> is included in the
                    <span class="code">[Cumulus]</span> section of the import configuration file being used then all of
                    the above fields will be set to <span class="code">None/null</span>. The default setting of the
                    <span class="code">calc_missing</span> option is <span class="code">True</span>
                </p>
            </li>
        </ul>

        <h3>Step-by-step instructions</h3>

        <p>To import observations from one or more Cumulus monthly log files:</p>

        <ol>
            <li>Ensure the Cumulus monthly log file(s) to be used for the import are located in a directory accessible
                by the machine that will run <span class="code">wee_import</span>. For the purposes of the following
                examples, there are nine monthly logs files covering the period October 2016 to June 2017, inclusive,
                located in the <span class="code">/var/tmp/cumulus</span> directory.
            </li>

            <li>Make a backup of the WeeWX database in case the import should go awry.
            </li>

            <li>Create an import configuration file. In this case we will make a copy of the example Cumulus import
                configuration file and save it as <span class="code">cumulus.conf</span> in the <span class="code">/var/tmp</span>
                directory:

                <pre class="tty cmd">$ cp /home/weewx/util/import/cumulus-example.conf /var/tmp/cumulus.conf
</pre>

            <li>Confirm that the <span class="code">source</span> option is set to Cumulus:
            <pre class="tty">source = Cumulus</pre>
            </li>


            <li>Confirm that the following options in the <span class="code">[Cumulus]</span> section are correctly set:

                <ul>
                    <li><a href="#cumulus_directory"><strong><span class="code">directory</span></strong></a>. The full
                        path to the directory containing the Cumulus monthly log files to be used as the source of the
                        imported data.
                    </li>
                    <li><a href="#cumulus_interval"><strong><span class="code">interval</span></strong></a>. Determines
                        how the WeeWX interval field is derived.
                    </li>
                    <li><a href="#cumulus_qc"><strong><span class="code">qc</span></strong></a>. Determines whether
                        quality control checks are performed on the imported data.
                    </li>
                    <li><a href="#cumulus_calc_missing"><strong><span class="code">calc_missing</span></strong></a>.
                        Determines whether missing derived observations will be calculated from the imported data.
                    </li>
                    <li><a href="#cumulus_separator"><strong><span class="code">separator</span></strong></a>. The date
                        field separator used in the Cumulus monthly log files.
                    </li>
                    <li><a href="#cumulus_delimiter"><strong><span class="code">delimiter</span></strong></a>. The field
                        delimiter used in the Cumulus monthly log files.
                    </li>
                    <li><a href="#cumulus_decimal"><strong><span class="code">decimal</span></strong></a>. The decimal
                        point character used in the Cumulus monthly log files.
                    </li>
                    <li><a href="#cumulus_ignore_invalid_data"><strong><span
                        class="code">ignore_invalid_data</span></strong></a>. Determines whether invalid data in a
                        source field is ignored or the import aborted.
                    </li>
                    <li><a href="#cumulus_tranche"><strong><span class="code">tranche</span></strong></a>. The number of
                        records written to the WeeWX database in each transaction.
                    </li>
                    <li><a href="#cumulus_UV"><strong><span class="code">UV_sensor</span></strong></a>. Whether a UV
                        sensor was installed when the source data was produced.
                    </li>
                    <li><a href="#cumulus_solar"><strong><span class="code">solar_sensor</span></strong></a>. Whether a
                        solar radiation sensor was installed when the source data was produced.
                    </li>
                    <li><a href="#cumulus_units"><strong><span class="code">[[Units]]</span></strong></a>. Defines the
                        units used in the Cumulus monthly log files.
                    </li>
                </ul>
            </li>

            <li>When first importing data it is prudent to do a dry run import before any data is actually imported. A
                dry run import will perform all steps of the import without actually writing imported data to the WeeWX
                database. In addition, consideration should be given to any additional options to be used such as <span
                    class="code">--date</span>.
                <p>To perform a dry run enter the following command:</p>

                <pre class="tty cmd">wee_import --import-config=/var/tmp/cumulus.conf --dry-run
</pre>

                <p>This will result in a short preamble with details on the data source, the destination of the imported
                    data and some other details on how the data will be processed. The import will then be performed but
                    no data will be written to the WeeWX database.
                </p>
                <p>The output should be similar to:</p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
Cumulus monthly log files in the '/var/tmp/cumulus' directory will be imported
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
This is a dry run, imported data will not be saved to archive.
Starting dry run import ...
Records covering multiple periods have been identified for import.
Period 1 ...
Unique records processed: 8858; Last timestamp: 2016-10-31 23:55:00 AEST (1477922100)
Period 2 ...
Unique records processed: 8636; Last timestamp: 2016-11-30 23:55:00 AEST (1480514100)
Period 3 ...
Unique records processed: 8925; Last timestamp: 2016-12-31 23:55:00 AEST (1483192500)
Period 4 ...
Unique records processed: 8908; Last timestamp: 2017-01-31 23:55:00 AEST (1485870900)
Period 5 ...
Unique records processed: 8029; Last timestamp: 2017-02-28 23:55:00 AEST (1488290100)
Period 6 ...
Unique records processed: 8744; Last timestamp: 2017-03-31 23:55:00 AEST (1490968500)
Period 7 ...
Unique records processed: 8489; Last timestamp: 2017-04-30 23:02:00 AEST (1493557320)
Period 8 ...
Unique records processed: 8754; Last timestamp: 2017-05-31 23:55:00 AEST (1496238900)
Period 9 ...
Unique records processed: 8470; Last timestamp: 2017-06-30 23:55:00 AEST (1498830900)
Finished dry run import
77813 records were processed and 77813 unique records would have been imported.
</pre>

                <p class="note">
                    <strong>Note</strong><br/>The nine periods correspond to the nine monthly log files used for this
                    import.
                </p>
                <p class="note">
                    <strong>Note</strong><br/>Any periods for which no data could be obtained will be skipped. The lack
                    of data may be due to a missing Cumulus monthly log file. A short explanatory note to this effect
                    will be displayed against the period concerned and an entry included in the log.
                </p>
            </li>

            <li>Once the dry run results are satisfactory the data can be imported using the following command:

                <pre class="tty cmd">wee_import --import-config=/var/tmp/cumulus.conf
</pre>

                <p>This will result in a preamble similar to that of a dry run. At the end of the preamble there will be
                    a prompt:
                </p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
Cumulus monthly log files in the '/var/tmp/cumulus' directory will be imported
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
Starting import ...
Records covering multiple periods have been identified for import.
Period 1 ...
Proceeding will save all imported records in the WeeWX archive.
Are you sure you want to proceed (y/n)?
</pre>

                <p>If there is more than one Cumulus monthly log file then <span class="code">wee_import</span> will
                    provide summary information on a per period basis during the import. In addition, if the <span
                        class="code">--date</span> option is used then source data that falls outside the date or date
                    range specified with the <span class="code">--date</span> option is ignored. In such cases the
                    preamble may look similar to:
                </p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
Cumulus monthly log files in the '/var/tmp/cumulus' directory will be imported
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
Starting import ...
Records covering multiple periods have been identified for import.
Period 1 ...
Period 1 - no records identified for import.
Period 2 ...
Period 2 - no records identified for import.
Period 3 ...
Proceeding will save all imported records in the WeeWX archive.
Are you sure you want to proceed (y/n)?
</pre>
            </li>

            <li>If the import parameters are acceptable enter <span class="code">y</span> to proceed with the import or
                <span class="code">n</span> to abort the import. If the import is confirmed, the source data will be
                imported, processed and saved in the WeeWX database. Information on the progress of the import will be
                displayed similar to the following:

                <pre class="tty">Unique records processed: 2305; Last timestamp: 2016-12-30 00:00:00 AEST (1483020000)
</pre>

                <p>Again if there is more than one Cumulus monthly log file and if the <span class="code">--date</span>
                    option is used then the progress information may instead look similar to:
                </p>

                <pre class="tty">Period 4 ...
Unique records processed: 8908; Last timestamp: 2017-01-31 23:55:00 AEST (1485870900)
Period 5 ...
Unique records processed: 8029; Last timestamp: 2017-02-28 23:55:00 AEST (1488290100)
Period 6 ...
Unique records processed: 8744; Last timestamp: 2017-03-31 23:55:00 AEST (1490968500)
</pre>

                <p class="note">
                    <strong>Note</strong><br/>Any periods for which no data could be obtained will be skipped. The lack
                    of data may be due to a missing Cumulus monthly log file. A short explanatory note to this effect
                    will be displayed against the period concerned and an entry included in the log.
                </p>
                <p>The line commencing with <span class="code">Unique records processed</span> should update as records
                    are imported with progress information on number of records processed, number of unique records
                    imported and the date time of the latest record processed. If the import spans multiple months (ie
                    multiple monthly log files) then a new <span class="code">Period</span> line is created for each
                    month.
                </p>

                <p>Once the initial import is complete <span class="code">wee_import</span> will, if requested, calculate
                    any missing derived observations and rebuild the daily summaries. A brief summary should be displayed
                    similar to the following:
                </p>

                <pre class="tty">Calculating missing derived observations ...
Processing record: 77782; Last record: 2017-06-30 00:00:00 AEST (1519826400)
Recalculating daily summaries...
Records processed: 77000; Last date: 2017-06-28 11:45:00 AEST (1519811100)
Finished recalculating daily summaries
Finished calculating missing derived observations
</pre>

                <p>When the import is complete a brief summary is displayed similar to the following:
                </p>

                <pre class="tty">Finished import
77813 records were processed and 77813 unique records imported in 106.96 seconds.
Those records with a timestamp already in the archive will not have been
imported. Confirm successful import in the WeeWX log file.
</pre>
            </li>

            <li>Whilst <span class="code">wee_import</span> will advise of the number of records processed and the
                number of unique records found, <span class="code">wee_import</span> does know how many, if any, of the
                imported records were successfully saved to the database. You should look carefully through the WeeWX
                log file covering the <span class="code">wee_import</span> session and take note of any records that
                were not imported. The most common reason for imported records not being saved to the database is
                because a record with that timestamp already exists in the database, in such cases something similar to
                the following will be found in the log:

                <pre class="tty">
Aug 22 14:38:28 stretch12 weewx[863]: manager: unable to add record 2018-09-04 04:20:00 AEST (1535998800) to database 'weewx.sdb': UNIQUE constraint failed: archive.dateTime
</pre>

                <p>In such cases take note of the timestamp of the record(s) concerned and make a decision about whether
                    to delete the pre-existing record and re-import the record or retain the pre-existing record.
                </p>
            </li>
        </ol>

        <h2>Importing from Weather Display</h2>

        <p class="warning">
            <strong>Warning!</strong><br/>Running WeeWX during a <span class="code">wee_import</span> session can lead to
            abnormal termination of the import. If WeeWX must remain running (e.g., so that live data is not lost) run
            the <span class="code">wee_import</span> session on another machine or to a second database and merge the
            in-use and second database once the import is complete.
        </p>

        <p><span class="code">wee_import</span> can import observational data from the one or more Weather Display monthly
            log files. Weather Display records observational data on a monthly basis in a number of either space delimited
            (.txt) and/or comma separated (.csv) text files. <span class="code">wee_import</span> can import observational
            data from the following Weather Display log files:
        </p>

        <ul>
            <li>MMYYYYlg.txt</li>
            <li>MMYYYYlgcsv.csv (csv format version of MMYYYYlg.txt)</li>
            <li>MMYYYYvantagelog.txt</li>
            <li>MMYYYYvantagelogcsv.csv (csv format version of MMYYYYvantagelog.txt)</li>
            <li>MMYYYYvantageextrasensorslog.csv</li>

        <p>where MM is a one or two digit month and YYYY is a four digit year</p>

        </ul>

        <p>The Weather Display monthly log files record observational data using a nominal 1 minute interval with each
            file recording various observations for the month and year designated by the MM and YYYY components of the
            file name. These files are accumulated over time and can be considered analogous to the WeeWX archive table.
            When <span class="code">wee_import</span> imports data from the Weather Display monthly log files each set
            of log files for a given month and year is considered a 'period'. <span class="code">wee_import</span>
            processes one period at a time in chronological order (oldest to newest) and provides import summary data on
            a per period basis.
        </p>

        <h3>Mapping data to archive fields</h3>

        <p>The WeeWX archive fields populated during the import of Weather Display data depends on the field mapping
            specified in <span class="code">[[FieldMap]]</span> stanza in the import configuration file. A given WeeWX
            field will be populated if:
        </p>

        <ul>
            <li>a valid field mapping exists,</li>
            <li>the WeeWX field exists in the WeeWX archive table schema, and</li>
            <li>the mapped Weather Display field contains valid data.</li>
        </ul>

        <p>The following WeeWX archive fields will be populated from other settings or configuration options and need not
            be included in the field map:
        </p>

            <ul>
                <li><span class="code">interval</span></li>
                <li><span class="code">usUnits</span></li>
            </ul>

            <li>The following WeeWX archive fields will be populated with values derived from the imported data provided
                <span class="code">calc_missing = True</span> is included in the <span class="code">[WD]</span>
                section of the import configuration file being used and the field exists in the in-use WeeWX archive
                table schema:

                <ul>
                    <li><span class="code">altimeter</span></li>
                    <li><span class="code">pressure</span></li>
                    <li><span class="code">rainRate</span></li>
                    <li><span class="code">windchill</span></li>
                </ul>

                <p class="note">
                    <strong>Note</strong><br/>If <span class="code">calc_missing = False</span> is included in the <span
                    class="code">[WD]</span> section of the import configuration file being used then all of the above
                    fields will be set to <span class="code">None/null</span>. The default setting of the <span
                    class="code">calc_missing</span> option is <span class="code">True</span>
                </p>
            </li>
        </ul>

        <h3>Step-by-step instructions</h3>

        <p>To import observations from one or more Weather Display monthly log files:</p>

        <ol>
            <li>Ensure the Weather Display monthly log file(s) to be used for the import are located in a directory
                accessible by the machine that will run <span class="code">wee_import</span>. For the purposes of the
                following examples, there are five months of logs files covering the period September 2018 to January 2019
                inclusive located in the <span class="code">/var/tmp/wd</span> directory.
            </li>

            <li>Make a backup of the WeeWX database in case the import should go awry.
            </li>

            <li>Create an import configuration file, this is easily done by making a copy of the example Weather Display
                import configuration file located in the <span class="code">/home/weewx/util/import</span> or
                <span class="code">/etc/weewx/import</span> directory as applicable. In this case we will make a copy of
                the example Weather Display import configuration file and save it as <span class="code">wd.conf</span> in
                the <span class="code">/var/tmp</span> directory:

                <pre class="tty cmd">$ cp /home/weewx/util/import/wd-example.conf /var/tmp/wd.conf</pre>
            </li>

            <li>Confirm that the <span class="code">source</span> option is set to WD:
            <pre class="tty">source = WD</pre>
            </li>


            <li>Confirm that the following options in the <span class="code">[WD]</span> section are correctly set:

                <ul>
                    <li><a href="#wd_directory"><strong><span class="code">directory</span></strong></a>. The full
                        path to the directory containing the Weather Display monthly log files to be used as the source
                        of the imported data.
                    </li>
                    <li><a href="#wd_logs_to_process"><strong><span class="code">logs_to_process</span></strong></a>.
                        Specifies the Weather Display monthly log files to be used to import data.
                    </li>
                    <li><a href="#wd_interval"><strong><span class="code">interval</span></strong></a>. Determines
                        how the WeeWX interval field is derived.
                    </li>
                    <li><a href="#wd_qc"><strong><span class="code">qc</span></strong></a>. Determines whether
                        quality control checks are performed on the imported data.
                    </li>
                    <li><a href="#wd_calc_missing"><strong><span class="code">calc_missing</span></strong></a>.
                        Determines whether missing derived observations will be calculated from the imported data.
                    </li>
                    <li><a href="#wd_txt_delimiter"><strong><span class="code">txt_delimiter</span></strong></a>. The
                        field delimiter used in the Weather Display space delimited (*.txt) monthly log files.
                    </li>
                    <li><a href="#wd_csv_delimiter"><strong><span class="code">csv_delimiter</span></strong></a>. The
                        field delimiter used in the Weather Display monthly comma separated values (*.csv) monthly
                        log files.
                    </li>
                    <li><a href="#wd_decimal"><strong><span class="code">decimal</span></strong></a>. The decimal
                        point character used in the Weather Display monthly log files.
                    </li>
                    <li><a href="#wd_ignore_missing_log"><strong><span
                        class="code">ignore_missing_log</span></strong></a>. Determines whether missing log files are to
                        be ignored or the import aborted.
                    </li>
                    <li><a href="#wd_ignore_invalid_data"><strong><span
                        class="code">ignore_invalid_data</span></strong></a>. Determines whether invalid data in a
                        source field is ignored or the import aborted.
                    </li>
                    <li><a href="#wd_tranche"><strong><span class="code">tranche</span></strong></a>. The number of
                        records written to the WeeWX database in each transaction.
                    </li>
                    <li><a href="#wd_UV"><strong><span class="code">UV_sensor</span></strong></a>. Whether a UV
                        sensor was installed when the source data was produced.
                    </li>
                    <li><a href="#wd_solar"><strong><span class="code">solar_sensor</span></strong></a>. Whether a
                        solar radiation sensor was installed when the source data was produced.
                    </li>
                    <li><a href="#wd_ignore_extreme_temp_hum"><strong><span class="code">ignore_extreme_temp_hum</span></strong></a>.
                        Determines whether temperature and humidity values of 255 will be ignored.
                    </li>
                    <li><a href="#wd_units"><strong><span class="code">[[Units]]</span></strong></a>. Defines the
                        units used in the Weather Display monthly log files.
                    </li>
                    <li><a href="#wd_fieldmap"><strong><span class="code">[[FieldMap]]</span></strong></a>. Defines the
                        mapping between imported data fields and WeeWX archive fields.
                    </li>
                </ul>
            </li>

            <li>When first importing data it is prudent to do a dry run import before any data is actually imported. A
                dry run import will perform all steps of the import without actually writing imported data to the WeeWX
                database. In addition, consideration should be given to any additional options to be used such
                as <span class="code">--date</span>.

                <p class="note">
                    <strong>Note</strong><br/>Due to some peculiarities of the Weather Display log structure it may be
                    prudent to use the <span class="code">--suppress--warnings</span> option during the initial dry run
                    so the overall progress of the import can be observed.
                </p>

                <p>To perform a dry run enter the following command:</p>

                <pre class="tty cmd">wee_import --import-config=/var/tmp/wd.conf --dry-run --suppress-warnings
</pre>

                <p>This will result in a short preamble with details on the data source, the destination of the imported
                    data and some other details on how the data will be processed. The import will then be performed but
                    no data will be written to the WeeWX database.
                </p>
                <p>The output should be similar to:</p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
Weather Display monthly log files in the '/var/tmp/WD' directory will be imported
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
This is a dry run, imported data will not be saved to archive.
Starting dry run import ...
Records covering multiple periods have been identified for import.
Period 1 ...
Unique records processed: 43183; Last timestamp: 2018-09-30 23:59:00 AEST (1538315940)
Period 2 ...
Unique records processed: 44620; Last timestamp: 2018-10-31 23:59:00 AEST (1540994340)
Period 3 ...
Unique records processed: 43136; Last timestamp: 2018-11-30 23:59:00 AEST (1543586340)
Period 4 ...
Unique records processed: 44633; Last timestamp: 2018-12-31 23:59:00 AEST (1546264740)
Period 5 ...
Unique records processed: 8977; Last timestamp: 2019-01-07 05:43:00 AEST (1546803780)
Finished dry run import
184765 records were processed and 184549 unique records would have been imported.
216 duplicate records were ignored.
</pre>

                <p class="note">
                    <strong>Note</strong><br/>The five periods correspond to the five months of log files used for this
                    import.
                </p>
                <p class="note">
                    <strong>Note</strong><br/>Any periods for which no data could be obtained will be skipped. The lack
                    of data may be due to a missing Weather Display log file. A short explanatory note to this effect
                    will be displayed against the period concerned and an entry included in the log.
                </p>
            </li>

            <li>If the <span class="code">--suppress--warnings</span> option was used it may be prudent to do a second
                dry run this time without the <span class="code">--suppress--warnings</span> option. This will allow any
                warnings generated by the dry run import to be observed:

                <pre class="tty cmd">wee_import --import-config=/var/tmp/wd.conf --dry-run</pre>

                <p>This will result in a short preamble with details on the data source, the destination of the imported
                    data and some other details on how the data will be processed. The import will then be performed but
                    no data will be written to the WeeWX database.
                </p>

                <p>The output should be similar to:</p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
Weather Display monthly log files in the '/var/tmp/WD' directory will be imported
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
This is a dry run, imported data will not be saved to archive.
Starting dry run import ...
Records covering multiple periods have been identified for import.
Period 1 ...
Warning: Import field 'radiation' is mapped to WeeWX field 'radiation' but the
         import field 'radiation' could not be found in one or more records.
         WeeWX field 'radiation' will be set to 'None' in these records.
Warning: Import field 'soiltemp' is mapped to WeeWX field 'soilTemp1' but the
         import field 'soiltemp' could not be found in one or more records.
         WeeWX field 'soilTemp1' will be set to 'None' in these records.
Warning: Import field 'soilmoist' is mapped to WeeWX field 'soilMoist1' but the
         import field 'soilmoist' could not be found in one or more records.
         WeeWX field 'soilMoist1' will be set to 'None' in these records.
Warning: Import field 'humidity' is mapped to WeeWX field 'outHumidity' but the
         import field 'humidity' could not be found in one or more records.
         WeeWX field 'outHumidity' will be set to 'None' in these records.
Warning: Import field 'heatindex' is mapped to WeeWX field 'heatindex' but the
         import field 'heatindex' could not be found in one or more records.
         WeeWX field 'heatindex' will be set to 'None' in these records.
Warning: Import field 'windspeed' is mapped to WeeWX field 'windSpeed' but the
         import field 'windspeed' could not be found in one or more records.
         WeeWX field 'windSpeed' will be set to 'None' in these records.
Warning: Import field 'barometer' is mapped to WeeWX field 'barometer' but the
         import field 'barometer' could not be found in one or more records.
         WeeWX field 'barometer' will be set to 'None' in these records.
Warning: Import field 'dewpoint' is mapped to WeeWX field 'dewpoint' but the
         import field 'dewpoint' could not be found in one or more records.
         WeeWX field 'dewpoint' will be set to 'None' in these records.
Warning: Import field 'rainlastmin' is mapped to WeeWX field 'rain' but the
         import field 'rainlastmin' could not be found in one or more records.
         WeeWX field 'rain' will be set to 'None' in these records.
Warning: Import field 'direction' is mapped to WeeWX field 'windDir' but the
         import field 'direction' could not be found in one or more records.
         WeeWX field 'windDir' will be set to 'None' in these records.
Warning: Import field 'temperature' is mapped to WeeWX field 'outTemp' but the
         import field 'temperature' could not be found in one or more records.
         WeeWX field 'outTemp' will be set to 'None' in these records.
Warning: Import field 'gustspeed' is mapped to WeeWX field 'windGust' but the
         import field 'gustspeed' could not be found in one or more records.
         WeeWX field 'windGust' will be set to 'None' in these records.
Unique records processed: 43183; Last timestamp: 2018-09-30 23:59:00 AEST (1538315940)
Period 2 ...
Unique records processed: 44620; Last timestamp: 2018-10-31 23:59:00 AEST (1540994340)
Period 3 ...
Unique records processed: 43136; Last timestamp: 2018-11-30 23:59:00 AEST (1543586340)
Period 4 ...
Unique records processed: 44633; Last timestamp: 2018-12-31 23:59:00 AEST (1546264740)
Period 5 ...
Unique records processed: 8977; Last timestamp: 2019-01-07 05:43:00 AEST (1546803780)
    6 duplicate records were identified in period 5:
        2019-01-04 10:31:00 AEST (1546561860)
        2019-01-04 10:32:00 AEST (1546561920)
        2019-01-04 10:33:00 AEST (1546561980)
        2019-01-04 10:34:00 AEST (1546562040)
        2019-01-04 10:35:00 AEST (1546562100)
        2019-01-04 10:36:00 AEST (1546562160)
Finished dry run import
184555 records were processed and 184549 unique records would have been imported.
6 duplicate records were ignored.
</pre>

                <p>In this case the following warnings are evident:</p>

                    <ul>
                        <li>
                            Period one had 12 warnings for import fields that were mapped to WeeWX data fields but for
                            which no data was found. This could be a sign that a complete month of data or a significant
                            portion of the month could be missing or it could be a case of just the first record of the
                            month is missing (a significant number of Weather Display monthly log files have been found
                            to be missing the first record of the month). In most cases this warning can be ignored.
                        </li>

                        <li>
                            Period five shows warnings for six entries in the period that have duplicate timestamps. This
                            could be a sign that there is a problem in one or more of the Weather Display monthly log
                            files for that month. However, anecdotally it has been found that duplicate entries often
                            exist in one or more Weather Display monthly log files. If the duplicates are to be ignored
                            then such warnings can be ignored otherwise the incorrect data should be removed from the
                            affected log files before import.
                        </li>
                    </ul>

            </li>

            <li>Once the dry run results are satisfactory the data can be imported using the following command:

                <pre class="tty cmd">wee_import --import-config=/var/tmp/wd.conf --suppress-warnings
</pre>

                <p class="note">
                    <strong>Note</strong><br/>The <span class="code">--suppress--warnings</span> option has been used to
                    suppress the previously encountered warnings.
                </p>

                <p>This will result in a preamble similar to that of a dry run. At the end of the preamble there will be
                    a prompt:
                </p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
Weather Display monthly log files in the '/var/tmp/WD' directory will be imported
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
Starting import ...
Records covering multiple periods have been identified for import.
Period 1 ...
Proceeding will save all imported records in the WeeWX archive.
Are you sure you want to proceed (y/n)?
</pre>

                <p>If there is more than one month of Weather Display monthly log files then
                    <span class="code">wee_import</span> will provide summary information on a per period basis during
                    the import. In addition, if the <span class="code">--date</span> option is used then source data
                    that falls outside the date or date range specified with the <span class="code">--date</span> option
                    is ignored. In such cases the preamble may look similar to:
                </p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
Weather Display monthly log files in the '/var/tmp/WD' directory will be imported
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
Observations timestamped after 2018-10-12 00:00:00 AEST (1539266400) and up to and
including 2018-10-13 00:00:00 AEST (1539352800) will be imported.
Starting import ...
Records covering multiple periods have been identified for import.
Period 1 ...
Period 1 - no records identified for import.
Period 2 ...
Proceeding will save all imported records in the WeeWX archive.
Are you sure you want to proceed (y/n)?
</pre>
            </li>

            <li>If the import parameters are acceptable enter <span class="code">y</span> to proceed with the import or
                <span class="code">n</span> to abort the import. If the import is confirmed, the source data will be
                imported, processed and saved in the WeeWX database. Information on the progress of the import will be
                displayed similar to the following:

                <pre class="tty">Unique records processed: 1250; Last timestamp: 2018-12-01 20:49:00 AEST (1543661340)
</pre>

                <p>Again if there is more than one month of Weather Display monthly log files and if the
                    <span class="code">--date</span> option is used then the progress information may instead look
                    similar to:
                </p>

                <pre class="tty">Period 2 ...
Unique records processed: 44620; Last timestamp: 2018-10-31 23:59:00 AEST (1540994340)
Period 3 ...
Unique records processed: 43136; Last timestamp: 2018-11-30 23:59:00 AEST (1543586340)
Period 4 ...
Unique records processed: 12000; Last timestamp: 2018-12-09 07:59:00 AEST (1544306340)
</pre>

                <p class="note">
                    <strong>Note</strong><br/>Any periods for which no data could be obtained will be skipped. The lack
                    of data may be due to a missing Weather Display log file. A short explanatory note to this effect
                    will be displayed against the period concerned and an entry included in the log.
                </p>

                <p>The line commencing with <span class="code">Unique records processed</span> should update as records
                    are imported with progress information on number of unique records processed and the date time of the
                    latest record processed. If the import spans multiple months then a new
                    <span class="code">Period</span> line is created for each month.
                </p>

                <p>Once the initial import is complete <span class="code">wee_import</span> will, if requested, calculate
                    any missing derived observations and rebuild the daily summaries. A brief summary should be displayed
                    similar to the following:
                </p>

                <pre class="tty">Calculating missing derived observations ...
Processing record: 184549; Last record: 2019-01-08 00:00:00 AEST (1546869600)
Recalculating daily summaries...
Records processed: 184000; Last date: 2019-01-06 20:34:00 AEST (1546770840)
Finished recalculating daily summaries
Finished calculating missing derived observations
</pre>

                <p>When the import is complete a brief summary is displayed similar to the following:
                </p>

                <pre class="tty">Finished import
184765 records were processed and 184549 unique records imported in 699.27 seconds.
216 duplicate records were ignored.
Those records with a timestamp already in the archive will not have been
imported. Confirm successful import in the WeeWX log file.
</pre>
            </li>

            <li>Whilst <span class="code">wee_import</span> will advise of the number of unique records imported,
                <span class="code">wee_import</span> does know how many, if any, of the imported records were successfully
                saved to the database. You should look carefully through the WeeWX log file covering the
                <span class="code">wee_import</span> session and take note of any records that were not imported. The most
                common reason for imported records not being saved to the database is because a record with that timestamp
                already exists in the database, in such cases something similar to the following will be found in the log:

                <pre class="tty">
Aug 22 14:38:28 stretch12 weewx[863]: manager: unable to add record 2018-09-04 04:20:00 AEST (1535998800) to database 'weewx.sdb': UNIQUE constraint failed: archive.dateTime
</pre>

                <p>In such cases take note of the timestamp of the record(s) concerned and make a decision about whether
                    to delete the pre-existing record and re-import the record or retain the pre-existing record.
                </p>
            </li>
        </ol>

        <h2>Importing from WeatherCat</h2>

        <p class="warning">
            <strong>Warning!</strong><br/>Running WeeWX during a <span class="code">wee_import</span> session can lead to
            abnormal termination of the import. If WeeWX must remain running (e.g., so that live data is not lost) run
            the <span class="code">wee_import</span> session on another machine or to a second database and merge the
            in-use and second database once the import is complete.
        </p>

        <p><span class="code">wee_import</span> can import observational data from the one or more WeatherCat monthly .cat
            files. A WeatherCat monthly .cat file records weather station observations for a single month. These files are
            accumulated over time and can be considered analogous to the WeeWX archive table.
            When <span class="code">wee_import</span> imports data from the WeatherCat monthly .cat files each file is
            considered a 'period'. <span class="code">wee_import</span> processes one period at a time in chronological
            order (oldest to newest) and provides import summary data on a per period basis.
        </p>

        <h3>Mapping data to archive fields</h3>

        <p>A WeatherCat import will populate the WeeWX archive fields as follows:</p>

        <ul>
            <li>Provided data exists for each field in the WeatherCat monthly .cat files, the following WeeWX archive
                fields will be directly populated by imported data:

                <ul>
                    <li><span class="code">dateTime</span></li>
                    <li><span class="code">barometer</span></li>
                    <li><span class="code">dewpoint</span></li>
                    <li><span class="code">heatindex</span></li>
                    <li><span class="code">inHumidity</span></li>
                    <li><span class="code">inTemp</span></li>
                    <li><span class="code">outHumidity</span></li>
                    <li><span class="code">outTemp</span></li>
                    <li><span class="code">radiation</span></li>
                    <li><span class="code">rain</span></li>
                    <li><span class="code">rainRate</span></li>
                    <li><span class="code">UV</span></li>
                    <li><span class="code">windDir</span></li>
                    <li><span class="code">windGust</span></li>
                    <li><span class="code">windSpeed</span></li>
                    <li><span class="code">windchill</span></li>
                </ul>

                <p class="note">
                    <strong>Note</strong><br/>If a field in the WeatherCat monthly .cat file has no data then the
                    corresponding WeeWX archive field will be set to <span class="code">None/null</span>.
                </p>
            </li>

            <li>The following WeeWX archive fields will be populated from other settings or configuration options:

                <ul>
                    <li><span class="code">interval</span></li>
                    <li><span class="code">usUnits</span></li>
                </ul>
            </li>

            <li>The following WeeWX archive fields will be populated with values derived from the imported data provided
                <span class="code">calc_missing = True</span> is included in the <span class="code">[WeatherCat]</span>
                section of the import configuration file being used and the field exists in the in-use WeeWX archive
                table schema.

                <ul>
                    <li><span class="code">altimeter</span></li>
                    <li><span class="code">ET</span></li>
                    <li><span class="code">pressure</span></li>
                </ul>

                <p class="note">
                    <strong>Note</strong><br/>If <span class="code">calc_missing = False</span> is included in the
                    <span class="code">[WeatherCat]</span> section of the import configuration file being used then all
                    of the above fields will be set to <span class="code">None/null</span>. The default setting of the
                    <span class="code">calc_missing</span> option is <span class="code">True</span>
                </p>
            </li>
        </ul>

        <h3>Step-by-step instructions</h3>

        <p>To import observations from one or more WeatherCat monthly .cat files:</p>

        <ol>
            <li>Ensure the WeatherCat monthly .cat file(s) to be used for the import are located in year directories with
                the year directories in turn located in a directory accessible by the machine that will
                run <span class="code">wee_import</span>. For the purposes of the following examples, there are nine
                monthly logs files covering the period October 2016 to June 2017 inclusive, located in
                the <span class="code">/var/tmp/wcat/2016</span> and <span class="code">/var/tmp/wcat/2017</span>
                directories respectively.
            </li>

            <li>Make a backup of the WeeWX database in case the import should go awry.
            </li>

            <li>Create an import configuration file. In this case we will make a copy of the example WeatherCat import
                configuration file and save it as <span class="code">wcat.conf</span> in the <span class="code">/var/tmp</span>
                directory:

                <pre class="tty cmd">$ cp /home/weewx/util/import/weathercat-example.conf /var/tmp/wcat.conf
</pre>

            <li>Confirm that the <span class="code">source</span> option is set to WeatherCat:
            <pre class="tty">source = WeatherCat</pre>
            </li>

            <li>Confirm that the following options in the <span class="code">[WeatherCat]</span> section are correctly set:

                <ul>
                    <li><a href="#wcat_directory"><strong><span class="code">directory</span></strong></a>. The full
                        path to the directory containing the directories containing the WeatherCat monthly .cat files to
                        be used as the source of the imported data.
                    </li>
                    <li><a href="#wcat_interval"><strong><span class="code">interval</span></strong></a>. Determines
                        how the WeeWX interval field is derived.
                    </li>
                    <li><a href="#wcat_qc"><strong><span class="code">qc</span></strong></a>. Determines whether
                        quality control checks are performed on the imported data.
                    </li>
                    <li><a href="#wcat_calc_missing"><strong><span class="code">calc_missing</span></strong></a>.
                        Determines whether missing derived observations will be calculated from the imported data.
                    </li>
                    <li><a href="#wcat_decimal"><strong><span class="code">decimal</span></strong></a>. The decimal
                        point character used in the WeatherCat monthly log files.
                    </li>
                    <li><a href="#wcat_tranche"><strong><span class="code">tranche</span></strong></a>. The number of
                        records written to the WeeWX database in each transaction.
                    </li>
                    <li><a href="#wcat_UV"><strong><span class="code">UV_sensor</span></strong></a>. Whether a UV
                        sensor was installed when the source data was produced.
                    </li>
                    <li><a href="#wcat_solar"><strong><span class="code">solar_sensor</span></strong></a>. Whether a
                        solar radiation sensor was installed when the source data was produced.
                    </li>
                    <li><a href="#wcat_units"><strong><span class="code">[[Units]]</span></strong></a>. Defines the
                        units used in the WeatherCat monthly .cat files.
                    </li>
                </ul>
            </li>

            <li>When first importing data it is prudent to do a dry run import before any data is actually imported. A
                dry run import will perform all steps of the import without actually writing imported data to the WeeWX
                database. In addition, consideration should be given to any additional options to be used such as <span
                    class="code">--date</span>.

                <p class="note">
                    <strong>Note</strong><br/>Whilst WeatherCat monthly .cat files use a fixed set of fields the
                    inclusion of fields other than <span class="code">t</span> (timestamp)
                    and <span class="code">V</span> (validation) is optional. For this reason the field map used for
                    WeatherCat imports includes fields that may not exist in some WeatherCat monthly .cat files
                    resulting in warnings by <span class="code">wee_port</span> that there may be missing data in the
                    import source. These warnings can be extensive and may detract from the ability of the user to monitor
                    the progress of the import. It may be prudent to use the <span class="code">--suppress--warnings</span>
                    option during the initial dry run so the overall progress of the import can be more easily observed.
                </p>

                <p>To perform a dry run enter the following command:</p>

                <pre class="tty cmd">wee_import --import-config=/var/tmp/wcat.conf --dry-run --suppress-warnings
</pre>

                <p>This will result in a short preamble with details on the data source, the destination of the imported
                    data and some other details on how the data will be processed. The import will then be performed but
                    no data will be written to the WeeWX database.
                </p>
                <p>The output should be similar to:</p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
WeatherCat monthly .cat files in the '/var/tmp/wcat' directory will be imported
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
This is a dry run, imported data will not be saved to archive.
Starting dry run import ...
Records covering multiple periods have been identified for import.
Period 1 ...
Unique records processed: 39555; Last timestamp: 2016-10-31 23:59:00 AEST (1477922340)
Period 2 ...
Unique records processed: 38284; Last timestamp: 2016-11-30 23:59:00 AEST (1480514340)
Period 3 ...
Unique records processed: 39555; Last timestamp: 2016-12-31 23:59:00 AEST (1483192740)
Period 4 ...
Unique records processed: 39555; Last timestamp: 2017-01-31 23:59:00 AEST (1485871140)
Period 5 ...
Unique records processed: 35598; Last timestamp: 2017-02-28 23:59:00 AEST (1488290340)
Period 6 ...
Unique records processed: 39555; Last timestamp: 2017-03-31 23:59:00 AEST (1490968740)
Period 7 ...
Unique records processed: 38284; Last timestamp: 2017-04-30 23:59:00 AEST (1493560740)
Period 8 ...
Unique records processed: 38284; Last timestamp: 2017-06-30 23:59:00 AEST (1498831140)
Finished dry run import
308670 records were processed and 308670 unique records would have been imported.
</pre>

                <p class="note">
                    <strong>Note</strong><br/>The eight periods correspond to the eight monthly .cat files used for this
                    import.
                </p>
                <p class="note">
                    <strong>Note</strong><br/>Any periods for which no data could be obtained will be skipped. The lack
                    of data may be due to a missing WeatherCat monthly .cat file. A short explanatory note to this effect
                    will be displayed against the period concerned and an entry included in the log.
                </p>
            </li>

            <li>If the <span class="code">--suppress--warnings</span> option was used it may be prudent to do a second
                dry run this time without the <span class="code">--suppress--warnings</span> option. This will allow any
                warnings generated by the dry run import to be observed:

                <pre class="tty cmd">wee_import --import-config=/var/tmp/wcat.conf --dry-run</pre>

                <p>This will result in a short preamble with details on the data source, the destination of the imported
                    data and some other details on how the data will be processed. The import will then be performed but
                    no data will be written to the WeeWX database.
                </p>

                <p>The output should be similar to:</p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
WeatherCat monthly .cat files in the '/var/tmp/wcat' directory will be imported
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
This is a dry run, imported data will not be saved to archive.
Starting dry run import ...
Records covering multiple periods have been identified for import.
Period 1 ...
Warning: Import field 'T1' is mapped to WeeWX field 'extraTemp1' but the
         import field 'T1' could not be found in one or more records.
         WeeWX field 'extraTemp1' will be set to 'None' in these records.
Warning: Import field 'T2' is mapped to WeeWX field 'extraTemp2' but the
         import field 'T2' could not be found in one or more records.
         WeeWX field 'extraTemp2' will be set to 'None' in these records.
Warning: Import field 'T3' is mapped to WeeWX field 'extraTemp3' but the
         import field 'T3' could not be found in one or more records.
         WeeWX field 'extraTemp3' will be set to 'None' in these records.
Warning: Import field 'H1' is mapped to WeeWX field 'extraHumid1' but the
         import field 'H1' could not be found in one or more records.
         WeeWX field 'extraHumid1' will be set to 'None' in these records.
Warning: Import field 'H2' is mapped to WeeWX field 'extraHumid2' but the
         import field 'H2' could not be found in one or more records.
         WeeWX field 'extraHumid2' will be set to 'None' in these records.
Warning: Import field 'Sm1' is mapped to WeeWX field 'soilMoist1' but the
         import field 'Sm1' could not be found in one or more records.
         WeeWX field 'soilMoist1' will be set to 'None' in these records.
Warning: Import field 'Sm2' is mapped to WeeWX field 'soilMoist2' but the
         import field 'Sm2' could not be found in one or more records.
         WeeWX field 'soilMoist2' will be set to 'None' in these records.
Warning: Import field 'Sm3' is mapped to WeeWX field 'soilMoist3' but the
         import field 'Sm3' could not be found in one or more records.
         WeeWX field 'soilMoist3' will be set to 'None' in these records.
Warning: Import field 'Sm4' is mapped to WeeWX field 'soilMoist4' but the
         import field 'Sm4' could not be found in one or more records.
         WeeWX field 'soilMoist4' will be set to 'None' in these records.
Warning: Import field 'Lw1' is mapped to WeeWX field 'leafWet1' but the
         import field 'Lw1' could not be found in one or more records.
         WeeWX field 'leafWet1' will be set to 'None' in these records.
Warning: Import field 'Lw2' is mapped to WeeWX field 'leafWet2' but the
         import field 'Lw2' could not be found in one or more records.
         WeeWX field 'leafWet2' will be set to 'None' in these records.
Warning: Import field 'St1' is mapped to WeeWX field 'soilTemp1' but the
         import field 'St1' could not be found in one or more records.
         WeeWX field 'soilTemp1' will be set to 'None' in these records.
Warning: Import field 'St2' is mapped to WeeWX field 'soilTemp2' but the
         import field 'St2' could not be found in one or more records.
         WeeWX field 'soilTemp2' will be set to 'None' in these records.
Warning: Import field 'St3' is mapped to WeeWX field 'soilTemp3' but the
         import field 'St3' could not be found in one or more records.
         WeeWX field 'soilTemp3' will be set to 'None' in these records.
Warning: Import field 'St4' is mapped to WeeWX field 'soilTemp4' but the
         import field 'St4' could not be found in one or more records.
         WeeWX field 'soilTemp4' will be set to 'None' in these records.
Warning: Import field 'Lt1' is mapped to WeeWX field 'leafTemp1' but the
         import field 'Lt1' could not be found in one or more records.
         WeeWX field 'leafTemp1' will be set to 'None' in these records.
Warning: Import field 'Lt2' is mapped to WeeWX field 'leafTemp2' but the
         import field 'Lt2' could not be found in one or more records.
         WeeWX field 'leafTemp2' will be set to 'None' in these records.
Unique records processed: 39555; Last timestamp: 2016-10-31 23:59:00 AEST (1477922340)
Period 2 ...
Warning: Import field 'T1' is mapped to WeeWX field 'extraTemp1' but the
         import field 'T1' could not be found in one or more records.
         WeeWX field 'extraTemp1' will be set to 'None' in these records.
Warning: Import field 'T2' is mapped to WeeWX field 'extraTemp2' but the
         import field 'T2' could not be found in one or more records.
         WeeWX field 'extraTemp2' will be set to 'None' in these records.
Warning: Import field 'T3' is mapped to WeeWX field 'extraTemp3' but the
         import field 'T3' could not be found in one or more records.
         WeeWX field 'extraTemp3' will be set to 'None' in these records.
Warning: Import field 'H1' is mapped to WeeWX field 'extraHumid1' but the
         import field 'H1' could not be found in one or more records.
         WeeWX field 'extraHumid1' will be set to 'None' in these records.
Warning: Import field 'H2' is mapped to WeeWX field 'extraHumid2' but the
         import field 'H2' could not be found in one or more records.
         WeeWX field 'extraHumid2' will be set to 'None' in these records.
Warning: Import field 'Sm1' is mapped to WeeWX field 'soilMoist1' but the
         import field 'Sm1' could not be found in one or more records.
         WeeWX field 'soilMoist1' will be set to 'None' in these records.
Warning: Import field 'Sm2' is mapped to WeeWX field 'soilMoist2' but the
         import field 'Sm2' could not be found in one or more records.
         WeeWX field 'soilMoist2' will be set to 'None' in these records.
Warning: Import field 'Sm3' is mapped to WeeWX field 'soilMoist3' but the
         import field 'Sm3' could not be found in one or more records.
         WeeWX field 'soilMoist3' will be set to 'None' in these records.
Warning: Import field 'Sm4' is mapped to WeeWX field 'soilMoist4' but the
         import field 'Sm4' could not be found in one or more records.
         WeeWX field 'soilMoist4' will be set to 'None' in these records.
Warning: Import field 'Lw1' is mapped to WeeWX field 'leafWet1' but the
         import field 'Lw1' could not be found in one or more records.
         WeeWX field 'leafWet1' will be set to 'None' in these records.
Warning: Import field 'Lw2' is mapped to WeeWX field 'leafWet2' but the
         import field 'Lw2' could not be found in one or more records.
         WeeWX field 'leafWet2' will be set to 'None' in these records.
Warning: Import field 'St1' is mapped to WeeWX field 'soilTemp1' but the
         import field 'St1' could not be found in one or more records.
         WeeWX field 'soilTemp1' will be set to 'None' in these records.
Warning: Import field 'St2' is mapped to WeeWX field 'soilTemp2' but the
         import field 'St2' could not be found in one or more records.
         WeeWX field 'soilTemp2' will be set to 'None' in these records.
Warning: Import field 'St3' is mapped to WeeWX field 'soilTemp3' but the
         import field 'St3' could not be found in one or more records.
         WeeWX field 'soilTemp3' will be set to 'None' in these records.
Warning: Import field 'St4' is mapped to WeeWX field 'soilTemp4' but the
         import field 'St4' could not be found in one or more records.
         WeeWX field 'soilTemp4' will be set to 'None' in these records.
Warning: Import field 'Lt1' is mapped to WeeWX field 'leafTemp1' but the
         import field 'Lt1' could not be found in one or more records.
         WeeWX field 'leafTemp1' will be set to 'None' in these records.
Warning: Import field 'Lt2' is mapped to WeeWX field 'leafTemp2' but the
         import field 'Lt2' could not be found in one or more records.
         WeeWX field 'leafTemp2' will be set to 'None' in these records.
Unique records processed: 38284; Last timestamp: 2016-11-30 23:59:00 AEST (1480514340)

... (identical entries for periods 3 to 7 omitted for conciseness)

Period 8 ...
Warning: Import field 'T1' is mapped to WeeWX field 'extraTemp1' but the
         import field 'T1' could not be found in one or more records.
         WeeWX field 'extraTemp1' will be set to 'None' in these records.
Warning: Import field 'T2' is mapped to WeeWX field 'extraTemp2' but the
         import field 'T2' could not be found in one or more records.
         WeeWX field 'extraTemp2' will be set to 'None' in these records.
Warning: Import field 'T3' is mapped to WeeWX field 'extraTemp3' but the
         import field 'T3' could not be found in one or more records.
         WeeWX field 'extraTemp3' will be set to 'None' in these records.
Warning: Import field 'H1' is mapped to WeeWX field 'extraHumid1' but the
         import field 'H1' could not be found in one or more records.
         WeeWX field 'extraHumid1' will be set to 'None' in these records.
Warning: Import field 'H2' is mapped to WeeWX field 'extraHumid2' but the
         import field 'H2' could not be found in one or more records.
         WeeWX field 'extraHumid2' will be set to 'None' in these records.
Warning: Import field 'Sm1' is mapped to WeeWX field 'soilMoist1' but the
         import field 'Sm1' could not be found in one or more records.
         WeeWX field 'soilMoist1' will be set to 'None' in these records.
Warning: Import field 'Sm2' is mapped to WeeWX field 'soilMoist2' but the
         import field 'Sm2' could not be found in one or more records.
         WeeWX field 'soilMoist2' will be set to 'None' in these records.
Warning: Import field 'Sm3' is mapped to WeeWX field 'soilMoist3' but the
         import field 'Sm3' could not be found in one or more records.
         WeeWX field 'soilMoist3' will be set to 'None' in these records.
Warning: Import field 'Sm4' is mapped to WeeWX field 'soilMoist4' but the
         import field 'Sm4' could not be found in one or more records.
         WeeWX field 'soilMoist4' will be set to 'None' in these records.
Warning: Import field 'Lw1' is mapped to WeeWX field 'leafWet1' but the
         import field 'Lw1' could not be found in one or more records.
         WeeWX field 'leafWet1' will be set to 'None' in these records.
Warning: Import field 'Lw2' is mapped to WeeWX field 'leafWet2' but the
         import field 'Lw2' could not be found in one or more records.
         WeeWX field 'leafWet2' will be set to 'None' in these records.
Warning: Import field 'St1' is mapped to WeeWX field 'soilTemp1' but the
         import field 'St1' could not be found in one or more records.
         WeeWX field 'soilTemp1' will be set to 'None' in these records.
Warning: Import field 'St2' is mapped to WeeWX field 'soilTemp2' but the
         import field 'St2' could not be found in one or more records.
         WeeWX field 'soilTemp2' will be set to 'None' in these records.
Warning: Import field 'St3' is mapped to WeeWX field 'soilTemp3' but the
         import field 'St3' could not be found in one or more records.
         WeeWX field 'soilTemp3' will be set to 'None' in these records.
Warning: Import field 'St4' is mapped to WeeWX field 'soilTemp4' but the
         import field 'St4' could not be found in one or more records.
         WeeWX field 'soilTemp4' will be set to 'None' in these records.
Warning: Import field 'Lt1' is mapped to WeeWX field 'leafTemp1' but the
         import field 'Lt1' could not be found in one or more records.
         WeeWX field 'leafTemp1' will be set to 'None' in these records.
Warning: Import field 'Lt2' is mapped to WeeWX field 'leafTemp2' but the
         import field 'Lt2' could not be found in one or more records.
         WeeWX field 'leafTemp2' will be set to 'None' in these records.
Unique records processed: 38284; Last timestamp: 2017-06-30 23:59:00 AEST (1498831140)
Finished dry run import
308670 records were processed and 308670 unique records would have been imported.
</pre>

                <p>In this case warnings are evident for numerous import/WeeWX field pairs that are mapped but for which
                    no data could be found. If the warnings relate to fields that are not included in the import source
                    data the warning may be safely ignored. If the warning relate to fields that the user expects to be
                    in the import source data the issue should be investigated further before the import is completed</p>

            </li>

            <li>Once the dry run results are satisfactory the data can be imported using the following command:

                <pre class="tty cmd">wee_import --import-config=/var/tmp/wcat.conf --suppress-warnings
</pre>

                <p>This will result in a preamble similar to that of a dry run. At the end of the preamble there will be
                    a prompt:
                </p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
WeatherCat monthly .cat files in the '/var/tmp/wcat' directory will be imported
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
Starting import ...
Records covering multiple periods have been identified for import.
Period 1 ...
Proceeding will save all imported records in the WeeWX archive.
Are you sure you want to proceed (y/n)?
</pre>

                <p>If there is more than one WeatherCat monthly .cat file then <span class="code">wee_import</span> will
                    provide summary information on a per period basis during the import. In addition, if
                    the <span class="code">--date</span> option is used then source data that falls outside the date or
                    date range specified with the <span class="code">--date</span> option is ignored. In such cases the
                    preamble may look similar to:
                </p>

                <pre class="tty">Using WeeWX configuration file /home/weewx/weewx.conf
Starting wee_import...
WeatherCat monthly .cat files in the '/var/tmp/wcat' directory will be imported
Using database binding 'wx_binding', which is bound to database 'weewx.sdb'
Destination table 'archive' unit system is '0x01' (US).
Missing derived observations will be calculated.
Starting import ...
Records covering multiple periods have been identified for import.
Period 1 ...
Period 1 - no records identified for import.
Period 2 ...
Period 2 - no records identified for import.
Period 3 ...
Proceeding will save all imported records in the WeeWX archive.
Are you sure you want to proceed (y/n)?
</pre>
            </li>

            <li>If the import parameters are acceptable enter <span class="code">y</span> to proceed with the import or
                <span class="code">n</span> to abort the import. If the import is confirmed, the source data will be
                imported, processed and saved in the WeeWX database. Information on the progress of the import will be
                displayed similar to the following:

                <pre class="tty">Unique records processed: 2305; Last timestamp: 2016-12-30 00:00:00 AEST (1483020000)
</pre>

                <p>Again if there is more than one WeatherCat monthly .cat file and if the <span class="code">--date</span>
                    option is used then the progress information may instead look similar to:
                </p>

                <pre class="tty">Period 4 ...
Unique records processed: 8908; Last timestamp: 2017-01-31 23:59:00 AEST (1485870900)
Period 5 ...
Unique records processed: 8029; Last timestamp: 2017-02-28 23:59:00 AEST (1488290100)
Period 6 ...
Unique records processed: 8744; Last timestamp: 2017-03-31 23:59:00 AEST (1490968500)
</pre>

                <p class="note">
                    <strong>Note</strong><br/>Any periods for which no data could be obtained will be skipped. The lack
                    of data may be due to a missing WeatherCat monthly .cat file. A short explanatory note to this effect
                    will be displayed against the period concerned and an entry included in the log.
                </p>
                <p>The line commencing with <span class="code">Unique records processed</span> should update as records
                    are imported with progress information on number of records processed, number of unique records
                    imported and the date time of the latest record processed. If the import spans multiple months (ie
                    multiple monthly .cat files) then a new <span class="code">Period</span> line is created for each
                    month.
                </p>

                <p>Once the initial import is complete <span class="code">wee_import</span> will, if requested, calculate
                    any missing derived observations and rebuild the daily summaries. A brief summary should be displayed
                    similar to the following:
                </p>

                <pre class="tty">Calculating missing derived observations ...
Processing record: 77782; Last record: 2017-06-30 00:00:00 AEST (1519826400)
Recalculating daily summaries...
Records processed: 77000; Last date: 2017-06-28 11:45:00 AEST (1519811100)
Finished recalculating daily summaries
Finished calculating missing derived observations
</pre>

                <p>When the import is complete a brief summary is displayed similar to the following:
                </p>

                <pre class="tty">Finished import
308670 records were processed and 308670 unique records imported in 1907.61 seconds.
Those records with a timestamp already in the archive will not have been
imported. Confirm successful import in the WeeWX log file.
</pre>
            </li>

            <li>Whilst <span class="code">wee_import</span> will advise of the number of records processed and the
                number of unique records found, <span class="code">wee_import</span> does know how many, if any, of the
                imported records were successfully saved to the database. You should look carefully through the WeeWX
                log file covering the <span class="code">wee_import</span> session and take note of any records that
                were not imported. The most common reason for imported records not being saved to the database is
                because a record with that timestamp already exists in the database, in such cases something similar to
                the following will be found in the log:

                <pre class="tty">
Aug 22 14:38:28 stretch12 weewx[863]: manager: unable to add record 2018-09-04 04:20:00 AEST (1535998800) to database 'weewx.sdb': UNIQUE constraint failed: archive.dateTime
</pre>

                <p>In such cases take note of the timestamp of the record(s) concerned and make a decision about whether
                    to delete the pre-existing record and re-import the record or retain the pre-existing record.
                </p>
            </li>
        </ol>

        <h2 id='import_failures'>Dealing with import failures</h2>

        <p>Sometimes bad things happen during an import.</p>

        <p>If errors were encountered, or if you suspect that the WeeWX database has been contaminated with incorrect
            data, here are some things you can try to fix things up.
        </p>

        <ul>
            <li>Manually delete the contaminated data. Use SQL commands to manipulate the data in the WeeWX archive
                database. The simplicity of this process will depend on your ability to use SQL, the amount of data
                imported, and whether the imported data was dispersed amongst existing. Once contaminated data have been
                removed the daily summary tables will need to be rebuilt using the <span
                    class="code">wee_database</span> utility.
            </li>

            <li>Delete the database and start over. For SQLite, simply delete the database file. For MySQL, drop the
                database. Then try the import again.
                <p class="warning">
                    <strong>Warning!</strong><br/>Deleting the database file or dropping the database will result in all
                    data in the database being lost.
                </p>
            </li>

            <li>If the above steps are not appropriate then the database should be restored from backup. You did make a
                backup before starting the import?
            </li>
        </ul>