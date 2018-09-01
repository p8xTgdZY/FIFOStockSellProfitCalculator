**FIFOStockSellProfitCalculator.py** calculates FIFO profits on a LibreOffice calc document with buys and sells in rows.    
    
PROFIT column will contain the "FIFO (First In First Out) profit" after a sell.   
Also the mean profit (see *TYPE_OF_CALCULATION*), or even FIFO and mean row blocks one after another can be calculated (see *changes_in_type_of_calculation* array).   
   
Each Row represents a single stock operation.   
The sell is identified by SELL string on TYPE column, using buys (BUY string on TYPE column) to acumulate previous buys.   
ASSET column contains the identification of the type of the asset: each different asset is treated apart.   
Other columns needed are PRICE, FEE, VOLUME and COST.   
PROFIT column will show FIFO profit (sell - FIFO buys) and on PROFIT\_DESC column the ASSET id will be repeated.   
Number of significant digits is DECIMAL\_NUMBER\_PRECISION, and asset volumes below ROUND\_IF\_BELOW are set to zero.   
See **Configuration** below.

See [this page for a step by step guide](https://circulosmeos.wordpress.com/2017/04/23/fifo-profits-stock-sell-calculation-with-libreoffice-calc).

Columns T, U and V and the three accumulated values have been written by the script on the sample file *sample.csv* (C1:J21):

![](https://circulosmeos.files.wordpress.com/2017/04/calc-stock-ops-example-after-script-exec.png?w=680)

Tested at least under LibreOffice versions 5 and 6.

Installation
============

Copy the file **FIFOStockSellProfitCalculator.py** to your LibreOffice python scripts directory.   
In Windows this would tipically be:

    C:\Program Files (x86)\LibreOffice 5\share\Scripts\python\

Then, it can be run on the current Calc document from Tools / Macros... 


Running as commandline script
=============================

If the LOG script variable is changed to "LOG = 1", useful internal operations are shown on stdout.   
In order to run the script from commandline, LibreOffice must be executed with special parameters:

    $ ./soffice.bin --calc \
    --accept="socket,host=localhost,port=2002;urp;StarOffice.ServiceManager"

Or in Windows:

    C:\Program Files (x86)\LibreOffice 5\program> soffice.exe --calc --accept="socket,host=localhost,port=2002;urp;"

Now, open the desired Calc document with the stocks operations.
Then, the *python* executable from LibreOffice installation must be used, so change your pwd to that path and append the path to this python script:

    C:\Program Files (x86)\LibreOffice 5\program> python.exe C:\FIFOStockSellProfitCalculator.py


Embed the script into a Calc document
=====================================

This script can be embedded into any LibreOffice Calc doc (.ods): [download this script to do that](https://github.com/circulosmeos/LibreOfficeScriptInsert).


Configuration
=============

Modify these script's variable values as convenient:

    # make FIFO (1), or average (0) calculation
    TYPE_OF_CALCULATION = 1;

    # array of changes in TYPE_OF_CALCULATION depending on rows:
    # pairs of the type [ row (from this number of row on, inclusive), TYPE_OF_CALCULATION]
    # Please, note that rows must be in order of magnitude in the array.
    # Example:
    # from row 13 on, change TYPE_OF_CALCULATION to 0, and from row 50 to the end change TYPE_OF_CALCULATION to 1:
    # changes_in_type_of_calculation = deque( [ [13, 0], [50, 1] ] )
    # Default value is no change from initial TYPE_OF_CALCULATION value:  = deque( [] )
    changes_in_type_of_calculation = deque( [] )

    # decimal numbers precision:
    DECIMAL_NUMBER_PRECISION = 6; # 6 significant digits (both integer & decimal places, depending of number size...)

    # regional comma separator:
    # DECIMAL_POINT is substitued by PYTHON_DECIMAL_POINT, just in case a regional configuration
    # be different from the standard decimal point '.'
    DECIMAL_POINT = '.' # change this to your regional configuration
    PYTHON_DECIMAL_POINT = '.'

    # column and value strings definitions:
    BEGINNING_OF_DATA = 2   # first row with data
    ASSET       = 'C'       # Unique asset id (i.e. USD, EUR, STCKXXXX, etc)
    TYPE        = 'E'       # SELL | BUY | other
    SELL        = 'sell'    # sell string identifier on TYPE column
    BUY         = 'buy'     # buy  string identifier on TYPE column
    PRICE       = 'G'
    FEE         = 'I'
    VOLUME      = 'J'
    PROFIT      = 'T'       # column of results
    PROFIT_DESC = 'U'       # here the ASSET id will be repeated
    ASSETS_VOL  = 'V'       # volume of assets of type ASSET after each buy/sell op
    BUY_TO_FIAT = 'W'       # volume of fiat currency (or origin asset) invested in the buy(s) now sold
    BUYS_TO_FIAT_VOLUME = 'A'   # volume of fiat currency (or origin asset) accumulated
                                # in buys of assets sold again to fiat currency (or origin asset)
                                # This column will be used in a row after the end of rows of data,
                                # in which a resume of all origin/dest assets will presented.
                                # If fiat currency (or origin asset) is the same in all cases,
                                # the rows can be added in a global result, but this is not done
                                # as a preventive measure to avoid errors of origin fiat/asset judgements.

    # round to zero if this quantity of assets is left on some sell:
    # NOTE: do no set ROUND_IF_BELOW to values below (previously set) DECIMAL_NUMBER_PRECISION 
    ROUND_IF_BELOW = Decimal('5e-06')

    # print logs to stdout
    LOG = 0


Examples
========

A sample csv file has been provided for testing purposes.


License
=======

Licensed as [GPL v3](http://www.gnu.org/licenses/gpl-3.0.en.html) or higher.   
