---
layout: page
title: Programming with Python
subtitle: Automating with Web Services
minutes: 30
---
> ## Learning Objectives {.objectives}
>
> *   Explain what a for loop does.
> *   Correctly write for loops to repeat simple calculations.
> *   Trace changes to a loop variable as the loop runs.
> *   Trace changes to other variables as they are updated by a for loop.


In the previous chapter, we developed a short script for loading some pre-processed streamgage data into Python using the Pandas library, converting some columns into the correct format, plotting the data, and saving the figures to files.

Our goal is to create a fully automated workflow that can produce plots for multiple stations without requiring any hand processing of the data. In this chapter, we are going to replace the initial import step in our previous code. Instead, we are going to request the data directly through the USGS web services with a short script.

Web services are automated tools for transfering data directly from machine to machine. Effectively, the code we write will ask a server for some data, and that server will hand it back to your code. The way that these commands and data are transferred is through an API (Application Programming Interface), which is essertially a set of functions and protocols for that interaction. We don't actually need to understand in detail what any of that means to use it, though - help pages are our friend!

Let's start by request data from the streamgage station at Lee's Ferry. We'll make a variable with the station number (which I looked up):


~~~ {.python}
station = 09380000
~~~
~~~ {.error}
  File "<ipython-input-1-6f8681f3257f>", line 1
    station = 09380000
                     ^
SyntaxError: invalid token
~~~

An integer cannot start with a zero, so Python doesn't know what we mean and produces a Syntax Error. Let's rewrite the station number as a string by putting it in quotes (single or double both work):

~~~ {.python}
station = '09380000'
~~~

We are interested in data between two specific dates:

~~~ {.python}
start_date = '2016-01-01'
end_date = '2016-01-10'
~~~

Data is accesed through APIs using URLs that contain the different pieces of information that the server needs to identify the data that we need. The USGS has a nifty little tool for helping us compose the URL:

http://waterservices.usgs.gov/rest/IV-Test-Tool.html

Let's use that tool to create the URL for the data we want:

http://waterservices.usgs.gov/nwis/iv/?format=rdb&sites=09380000&startDT=2016-01-01&endDT=2016-01-10&parameterCd=00060,00065

If we go to that URL, we could just download that file and process it manually. We don't want to do that.

Let's start by making a variable `url` for the URL of the data we want:

~~~ {.python}
url = 'http://waterservices.usgs.gov/nwis/iv/?format=rdb&sites=09380000&startDT=2016-01-01&endDT=2016-01-10&parameterCd=00060,00065'
~~~
~~~ {.output}
We previously used the Pandas method `read_csv` to load a comma-delimited file into Python. That method can also receive a URL as the address to the file:
~~~

~~~ {.python}
import pandas as pd

pd.read_csv(url)
~~~
~~~ {.error}
---------------------------------------------------------------------------

CParserError                              Traceback (most recent call last)

<ipython-input-5-ab590da0ab09> in <module>()
      1 import pandas as pd
      2 
----> 3 pd.read_csv(url)


//anaconda/lib/python2.7/site-packages/pandas/io/parsers.pyc in parser_f(filepath_or_buffer, sep, dialect, compression, doublequote, escapechar, quotechar, quoting, skipinitialspace, lineterminator, header, index_col, names, prefix, skiprows, skipfooter, skip_footer, na_values, true_values, false_values, delimiter, converters, dtype, usecols, engine, delim_whitespace, as_recarray, na_filter, compact_ints, use_unsigned, low_memory, buffer_lines, warn_bad_lines, error_bad_lines, keep_default_na, thousands, comment, decimal, parse_dates, keep_date_col, dayfirst, date_parser, memory_map, float_precision, nrows, iterator, chunksize, verbose, encoding, squeeze, mangle_dupe_cols, tupleize_cols, infer_datetime_format, skip_blank_lines)
    496                     skip_blank_lines=skip_blank_lines)
    497 
--> 498         return _read(filepath_or_buffer, kwds)
    499 
    500     parser_f.__name__ = name


//anaconda/lib/python2.7/site-packages/pandas/io/parsers.pyc in _read(filepath_or_buffer, kwds)
    283         return parser
    284 
--> 285     return parser.read()
    286 
    287 _parser_defaults = {


//anaconda/lib/python2.7/site-packages/pandas/io/parsers.pyc in read(self, nrows)
    745                 raise ValueError('skip_footer not supported for iteration')
    746 
--> 747         ret = self._engine.read(nrows)
    748 
    749         if self.options.get('as_recarray'):


//anaconda/lib/python2.7/site-packages/pandas/io/parsers.pyc in read(self, nrows)
   1195     def read(self, nrows=None):
   1196         try:
-> 1197             data = self._reader.read(nrows)
   1198         except StopIteration:
   1199             if self._first_chunk:


pandas/parser.pyx in pandas.parser.TextReader.read (pandas/parser.c:7988)()


pandas/parser.pyx in pandas.parser.TextReader._read_low_memory (pandas/parser.c:8244)()


pandas/parser.pyx in pandas.parser.TextReader._read_rows (pandas/parser.c:8970)()


pandas/parser.pyx in pandas.parser.TextReader._tokenize_rows (pandas/parser.c:8838)()


pandas/parser.pyx in pandas.parser.raise_parser_error (pandas/parser.c:22649)()


CParserError: Error tokenizing data. C error: Expected 1 fields in line 12, saw 2
~~~

Pandas is unhappy and cannot load our file. The parser, a script responsible for converting the text in the file into a data structure inside Python, expects one line of column titles and then data with the same number of columns. Open the URL in a separate tab in your web browser and examine the file. The header is making it appear like the number of columns is charging part way through the file.

The most widely used libraries in Python are very well documented. The help file for the specific function we are trying to use will contain information about the options that might let us read the file while ignoring the header:


~~~ {.python}
help(pd.read_csv)
~~~
~~~ {.output}
    Help on function read_csv in module pandas.io.parsers:
    
    read_csv(filepath_or_buffer, sep=',', dialect=None, compression='infer', doublequote=True, escapechar=None, quotechar='"', quoting=0, skipinitialspace=False, lineterminator=None, header='infer', index_col=None, names=None, prefix=None, skiprows=None, skipfooter=None, skip_footer=0, na_values=None, true_values=None, false_values=None, delimiter=None, converters=None, dtype=None, usecols=None, engine=None, delim_whitespace=False, as_recarray=False, na_filter=True, compact_ints=False, use_unsigned=False, low_memory=True, buffer_lines=None, warn_bad_lines=True, error_bad_lines=True, keep_default_na=True, thousands=None, comment=None, decimal='.', parse_dates=False, keep_date_col=False, dayfirst=False, date_parser=None, memory_map=False, float_precision=None, nrows=None, iterator=False, chunksize=None, verbose=False, encoding=None, squeeze=False, mangle_dupe_cols=True, tupleize_cols=False, infer_datetime_format=False, skip_blank_lines=True)
        Read CSV (comma-separated) file into DataFrame
        
        Also supports optionally iterating or breaking of the file
        into chunks.
        
        Additional help can be found in the `online docs for IO Tools
        <http://pandas.pydata.org/pandas-docs/stable/io.html>`_.
        
        Parameters
        ----------
        filepath_or_buffer : string or file handle / StringIO
            The string could be a URL. Valid URL schemes include
            http, ftp, s3, and file. For file URLs, a
            host is expected. For instance, a local file could be
            file ://localhost/path/to/table.csv
        sep : string, default ','
            Delimiter to use. If sep is None, will try to automatically determine
            this. Regular expressions are accepted.
        engine : {'c', 'python'}
            Parser engine to use. The C engine is faster while the python engine is
            currently more feature-complete.
        lineterminator : string (length 1), default None
            Character to break file into lines. Only valid with C parser
        quotechar : string (length 1)
            The character used to denote the start and end of a quoted item. Quoted
            items can include the delimiter and it will be ignored.
        quoting : int or csv.QUOTE_* instance, default None
            Control field quoting behavior per ``csv.QUOTE_*`` constants. Use one of
            QUOTE_MINIMAL (0), QUOTE_ALL (1), QUOTE_NONNUMERIC (2) or QUOTE_NONE (3).
            Default (None) results in QUOTE_MINIMAL behavior.
        skipinitialspace : boolean, default False
            Skip spaces after delimiter
        escapechar : string (length 1), default None
            One-character string used to escape delimiter when quoting is QUOTE_NONE.
        dtype : Type name or dict of column -> type, default None
            Data type for data or columns. E.g. {'a': np.float64, 'b': np.int32}
            (Unsupported with engine='python')
        compression : {'gzip', 'bz2', 'infer', None}, default 'infer'
            For on-the-fly decompression of on-disk data. If 'infer', then use gzip or
            bz2 if filepath_or_buffer is a string ending in '.gz' or '.bz2',
            respectively, and no decompression otherwise. Set to None for no
            decompression.
        dialect : string or csv.Dialect instance, default None
            If None defaults to Excel dialect. Ignored if sep longer than 1 char
            See csv.Dialect documentation for more details
        header : int, list of ints, default 'infer'
            Row number(s) to use as the column names, and the start of the
            data.  Defaults to 0 if no ``names`` passed, otherwise ``None``. Explicitly
            pass ``header=0`` to be able to replace existing names. The header can be
            a list of integers that specify row locations for a multi-index on the
            columns E.g. [0,1,3]. Intervening rows that are not specified will be
            skipped (e.g. 2 in this example are skipped). Note that this parameter
            ignores commented lines and empty lines if ``skip_blank_lines=True``, so header=0
            denotes the first line of data rather than the first line of the file.
        skiprows : list-like or integer, default None
            Line numbers to skip (0-indexed) or number of lines to skip (int)
            at the start of the file
        index_col : int or sequence or False, default None
            Column to use as the row labels of the DataFrame. If a sequence is given, a
            MultiIndex is used. If you have a malformed file with delimiters at the end
            of each line, you might consider index_col=False to force pandas to _not_
            use the first column as the index (row names)
        names : array-like, default None
            List of column names to use. If file contains no header row, then you
            should explicitly pass header=None
        prefix : string, default None
            Prefix to add to column numbers when no header, e.g 'X' for X0, X1, ...
        na_values : str, list-like or dict, default None
            Additional strings to recognize as NA/NaN. If dict passed, specific
            per-column NA values
        true_values : list, default None
            Values to consider as True
        false_values : list, default None
            Values to consider as False
        keep_default_na : bool, default True
            If na_values are specified and keep_default_na is False the default NaN
            values are overridden, otherwise they're appended to
        parse_dates : boolean, list of ints or names, list of lists, or dict, default False
            If True -> try parsing the index.
            If [1, 2, 3] -> try parsing columns 1, 2, 3 each as a separate date column.
            If [[1, 3]] -> combine columns 1 and 3 and parse as a single date column.
            {'foo' : [1, 3]} -> parse columns 1, 3 as date and call result 'foo'
            A fast-path exists for iso8601-formatted dates.
        keep_date_col : boolean, default False
            If True and parse_dates specifies combining multiple columns then
            keep the original columns.
        date_parser : function, default None
            Function to use for converting a sequence of string columns to an
            array of datetime instances. The default uses dateutil.parser.parser
            to do the conversion. Pandas will try to call date_parser in three different
            ways, advancing to the next if an exception occurs: 1) Pass one or more arrays
            (as defined by parse_dates) as arguments; 2) concatenate (row-wise) the string
            values from the columns defined by parse_dates into a single array and pass
            that; and 3) call date_parser once for each row using one or more strings
            (corresponding to the columns defined by parse_dates) as arguments.
        dayfirst : boolean, default False
            DD/MM format dates, international and European format
        thousands : str, default None
            Thousands separator
        comment : str, default None
            Indicates remainder of line should not be parsed. If found at the
            beginning of a line, the line will be ignored altogether. This parameter
            must be a single character. Like empty lines (as long as ``skip_blank_lines=True``),
            fully commented lines are ignored by the parameter `header`
            but not by `skiprows`. For example, if comment='#', parsing
            '#empty\na,b,c\n1,2,3' with `header=0` will result in 'a,b,c' being
            treated as the header.
        decimal : str, default '.'
            Character to recognize as decimal point. E.g. use ',' for European data
        nrows : int, default None
            Number of rows of file to read. Useful for reading pieces of large files
        iterator : boolean, default False
            Return TextFileReader object for iteration or getting chunks with ``get_chunk()``.
        chunksize : int, default None
            Return TextFileReader object for iteration. `See IO Tools docs for more
            information
            <http://pandas.pydata.org/pandas-docs/stable/io.html#io-chunking>`_ on
            ``iterator`` and ``chunksize``.
        skipfooter : int, default 0
            Number of lines at bottom of file to skip (Unsupported with engine='c')
        converters : dict, default None
            Dict of functions for converting values in certain columns. Keys can either
            be integers or column labels
        verbose : boolean, default False
            Indicate number of NA values placed in non-numeric columns
        delimiter : string, default None
            Alternative argument name for sep. Regular expressions are accepted.
        encoding : string, default None
            Encoding to use for UTF when reading/writing (ex. 'utf-8'). `List of Python
            standard encodings
            <https://docs.python.org/3/library/codecs.html#standard-encodings>`_
        squeeze : boolean, default False
            If the parsed data only contains one column then return a Series
        na_filter : boolean, default True
            Detect missing value markers (empty strings and the value of na_values). In
            data without any NAs, passing na_filter=False can improve the performance
            of reading a large file
        usecols : array-like, default None
            Return a subset of the columns.
            Results in much faster parsing time and lower memory usage.
        mangle_dupe_cols : boolean, default True
            Duplicate columns will be specified as 'X.0'...'X.N', rather than 'X'...'X'
        tupleize_cols : boolean, default False
            Leave a list of tuples on columns as is (default is to convert to
            a Multi Index on the columns)
        error_bad_lines : boolean, default True
            Lines with too many fields (e.g. a csv line with too many commas) will by
            default cause an exception to be raised, and no DataFrame will be returned.
            If False, then these "bad lines" will dropped from the DataFrame that is
            returned. (Only valid with C parser)
        warn_bad_lines : boolean, default True
            If error_bad_lines is False, and warn_bad_lines is True, a warning for each
            "bad line" will be output. (Only valid with C parser).
        infer_datetime_format : boolean, default False
            If True and parse_dates is enabled for a column, attempt to infer
            the datetime format to speed up the processing
        skip_blank_lines : boolean, default True
            If True, skip over blank lines rather than interpreting as NaN values
        
        Returns
        -------
        result : DataFrame or TextParser
~~~    


A way to see the documentation as a separate window within an iPython notebook is with a question mark:

~~~ {.python}
pd.read_csv?
~~~

The function `read_csv` requires the first parameter (a filepath) and accepts many optional parameters (if not set, they use a default value). For example, `sep=','` is an optional function parameter that defines the column separator. By default, it's a comma.

Let's try loading our file again. Because the columns in our file are separated by tabs, we need to set the `sep` parameter. To remove the header, we will also tell Pandas that lines that start with `#` are comments and it should not import them.


~~~ {.python}
data = pd.read_csv(url, sep='\t', comment='#')
data.head()
~~~
~~~ {.output}
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>agency_cd</th>
      <th>site_no</th>
      <th>datetime</th>
      <th>tz_cd</th>
      <th>01_00060</th>
      <th>01_00060_cd</th>
      <th>02_00065</th>
      <th>02_00065_cd</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>5s</td>
      <td>15s</td>
      <td>20d</td>
      <td>6s</td>
      <td>14n</td>
      <td>10s</td>
      <td>14n</td>
      <td>10s</td>
    </tr>
    <tr>
      <th>1</th>
      <td>USGS</td>
      <td>09380000</td>
      <td>2016-01-01 00:00</td>
      <td>MST</td>
      <td>15200</td>
      <td>P</td>
      <td>9.74</td>
      <td>P</td>
    </tr>
    <tr>
      <th>2</th>
      <td>USGS</td>
      <td>09380000</td>
      <td>2016-01-01 00:15</td>
      <td>MST</td>
      <td>14900</td>
      <td>P</td>
      <td>9.67</td>
      <td>P</td>
    </tr>
    <tr>
      <th>3</th>
      <td>USGS</td>
      <td>09380000</td>
      <td>2016-01-01 00:30</td>
      <td>MST</td>
      <td>14600</td>
      <td>P</td>
      <td>9.62</td>
      <td>P</td>
    </tr>
    <tr>
      <th>4</th>
      <td>USGS</td>
      <td>09380000</td>
      <td>2016-01-01 00:45</td>
      <td>MST</td>
      <td>14200</td>
      <td>P</td>
      <td>9.55</td>
      <td>P</td>
    </tr>
  </tbody>
</table>
</div>
~~~

There's our file! Pandas read the first row of the file as the column headers and everything below that as data, but row 0 doesn't belong. We can tell the function `read_csv` to use the second row of the file as the column headers and ignore the first row so at least our data is imported correctly:

~~~ {.python}
data = pd.read_csv(url, header=1, sep='\t', comment='#')
data.head()
~~~
~~~ {.output}
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>5s</th>
      <th>15s</th>
      <th>20d</th>
      <th>6s</th>
      <th>14n</th>
      <th>10s</th>
      <th>14n.1</th>
      <th>10s.1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>USGS</td>
      <td>9380000</td>
      <td>2016-01-01 00:00</td>
      <td>MST</td>
      <td>15200</td>
      <td>P</td>
      <td>9.74</td>
      <td>P</td>
    </tr>
    <tr>
      <th>1</th>
      <td>USGS</td>
      <td>9380000</td>
      <td>2016-01-01 00:15</td>
      <td>MST</td>
      <td>14900</td>
      <td>P</td>
      <td>9.67</td>
      <td>P</td>
    </tr>
    <tr>
      <th>2</th>
      <td>USGS</td>
      <td>9380000</td>
      <td>2016-01-01 00:30</td>
      <td>MST</td>
      <td>14600</td>
      <td>P</td>
      <td>9.62</td>
      <td>P</td>
    </tr>
    <tr>
      <th>3</th>
      <td>USGS</td>
      <td>9380000</td>
      <td>2016-01-01 00:45</td>
      <td>MST</td>
      <td>14200</td>
      <td>P</td>
      <td>9.55</td>
      <td>P</td>
    </tr>
    <tr>
      <th>4</th>
      <td>USGS</td>
      <td>9380000</td>
      <td>2016-01-01 01:00</td>
      <td>MST</td>
      <td>14000</td>
      <td>P</td>
      <td>9.49</td>
      <td>P</td>
    </tr>
  </tbody>
</table>
</div>
~~~

Of course, the column headers are now wrong! We were planning to rename them, so let's just do that during import:

~~~ {.python}
new_column_names = ['Agency', 'Station', 'OldDateTime', 'Timezone', 'Discharge_cfs', 'Discharge_stat', 'Stage_ft', 'Stage_stat']

data = pd.read_csv(url, header=1, sep='\t', comment='#', names = new_column_names)
data.head()
~~~
~~~ {.output}
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Agency</th>
      <th>Station</th>
      <th>OldDateTime</th>
      <th>Timezone</th>
      <th>Discharge_cfs</th>
      <th>Discharge_stat</th>
      <th>Stage_ft</th>
      <th>Stage_stat</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>USGS</td>
      <td>9380000</td>
      <td>2016-01-01 00:00</td>
      <td>MST</td>
      <td>15200</td>
      <td>P</td>
      <td>9.74</td>
      <td>P</td>
    </tr>
    <tr>
      <th>1</th>
      <td>USGS</td>
      <td>9380000</td>
      <td>2016-01-01 00:15</td>
      <td>MST</td>
      <td>14900</td>
      <td>P</td>
      <td>9.67</td>
      <td>P</td>
    </tr>
    <tr>
      <th>2</th>
      <td>USGS</td>
      <td>9380000</td>
      <td>2016-01-01 00:30</td>
      <td>MST</td>
      <td>14600</td>
      <td>P</td>
      <td>9.62</td>
      <td>P</td>
    </tr>
    <tr>
      <th>3</th>
      <td>USGS</td>
      <td>9380000</td>
      <td>2016-01-01 00:45</td>
      <td>MST</td>
      <td>14200</td>
      <td>P</td>
      <td>9.55</td>
      <td>P</td>
    </tr>
    <tr>
      <th>4</th>
      <td>USGS</td>
      <td>9380000</td>
      <td>2016-01-01 01:00</td>
      <td>MST</td>
      <td>14000</td>
      <td>P</td>
      <td>9.49</td>
      <td>P</td>
    </tr>
  </tbody>
</table>
</div>
~~~


> ## Why not just delete the bad row? {.callout}
> 
> In the previous lesson, we saw we could create new DataFrames that contained just a subset of the rows of an existing DataFrame. Why did we go through so much trouble to avoid importing the "bad" row in with the data?
> 
> One consideration is memory space and speed. If we had simply copied the good rows into a new DataFrame, Python would have created a new copy of the data, occupying available memory and taking some time. While the extra computational expenses are negligible for these small datasets, they quickly add up! By fixing those issues as we are importing the data, we create only one copy of the DataFrame from the start.
> 
> The second concern is less obvious but could potentially complicate how we handle the data later on. Each column in the DataFrame has a data type that restricts what can be done with the data (check them with `data.dtypes`). Columns of the type `object`, for example, contain strings and cannot be used in mathematical expressions. When the "bad" row was included in the data, the first entry in the columns for stage and discharge were "14n", forcing Pandas to consider every entry in those columns to be a string instead of an integer or float.

Our script can now import data through the USGS web services in a format that we can use with our existing code! Let's combine these commands with the code we wrote in the previous lesson. We can copy the old code, comment out (with the symbol #) any rows that we don't want to use, and add our new commands:

~~~ {.python}
import pandas as pd
import matplotlib.pyplot as plt
%matplotlib inline

# data = pd.read_csv('data/streamgage.csv')

new_column_names = ['Agency', 'Station', 'OldDateTime', 'Timezone', 'Discharge_cfs', 'Discharge_stat', 'Stage_ft', 'Stage_stat']
# data.columns = new_column_names

url = 'http://waterservices.usgs.gov/nwis/iv/?format=rdb&sites=09380000&startDT=2016-01-01&endDT=2016-01-10&parameterCd=00060,00065'
data = pd.read_csv(url, header=1, sep='\t', comment='#', names = new_column_names)

data['DateTime'] = pd.to_datetime(data['OldDateTime'])

new_station_name = "0" + str(data['Station'].unique()[0])
data['Station'] = new_station_name

data.plot(x='DateTime', y='Discharge_cfs', title='Station ' + new_station_name)
plt.xlabel('Time')
plt.ylabel('Discharge (cfs)')
plt.savefig('data/discharge_' + new_station_name + '.png')
plt.show()
~~~
~~~ {.output}
![png](fig/output_23_0.png)
~~~
