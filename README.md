> **Note:** CUPS 2.4.0 added native support for AirPrint so this project
> is no longer required.
>
> For iOS to see and automatically set the correct default paper size,
> set the options `DefaultPaperSize` and `ReadyPaperSizes` and be
> aware that the CUPS cache (e.g. `/var/cache/cups`) may need to be
> cleared and CUPS restarted for the changes to be effective.
>
> For details and commands for debugging, see
> https://github.com/apple/cups/issues/6009#issuecomment-1099290442 and
> subsequent comments in this thread.

## airprint-generate.py

This script will generate avahi .service files for shared CUPS printers.

This script will connect to a CUPS server and for each printer configured and
marked as shared will generate a .service file for avahi that is compatible
with Apple's AirPrint announcements. Any printer that can be configured to work
with CUPS can be used. Printers should not be configured in CUPS as raw, unless
the printer can natively print PDF. That is to say, CUPS needs to already be
configured with a PDF filter. Debian based distributions ship CUPS pre-configured
this way.

DNSSD has a limit of 255 Chars for a given txt-record, because of this the list
of accepted pdl's will be truncated to fit. If you're curious to see which ones
are trimmed out of the list run with the script with the verbose flag (--verbose)

If python-lxml is installed, .service files will be generated in a human
readble format, I wasn't able to get minidom's version to work acceptably.

### Usage: airprint-generate.py [options]

Options:
  -h, --help            show this help message and exit
  -H HOSTNAME, --host=HOSTNAME
                        Hostname of CUPS server (optional)
  -P PORT, --port=PORT  Port number of CUPS server
  -u USER, --user=USER  Username to authenticate with against CUPS
  -d DIRECTORY, --directory=DIRECTORY
                        Directory to create service files
  -v, --verbose         Print debugging information to STDERR
  -p PREFIX, --prefix=PREFIX
                        Prefix all files with this string
## Docker containerized avahi .service generation

After the printers have been configured in the cups server, docker can interactively generate the avahi .service
while making use of the container.

* Build the container

```shell
docker build -t airprint-generate .
```

* Generate the avahi service by defining the ip address of the cups server

```shell
docker run --rm -it -v $(pwd):/tmp airprint-generate -H ${CUPS_SERVER_IP} -d /tmp
```
