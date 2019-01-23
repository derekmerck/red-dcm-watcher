Remote Embedded DIANA Watcher
==========================

Derek Merck  
<derek_merck@brown.edu>  
Rhode Island Hospital and Brown University  
Providence, RI  

DICOM watch and report service using Orthanc and DIANA, packaged with docker-compose for [belena.io][] arm32v7 devices.

[balena.io]: https://www.balena.io


Configuration
--------------

Configuration is handled through the services definitions, which can be passed as a file or as an environment variable to the DIANA service.

For a default reporting service that triggers every 3 minutes and reports all studies, set device environment variables for the following:

```bash
ORTHANC_PASSWORD=<password>
LOCAL_PACS_ADDR="pacs,<aet>,<addr>,<port>"
SPLUNK_HOST=<splunk host name>
SPLUNK_HEC_TOKEN=<registered hec token>
```

Any other sensitive information should also be set via environment variables and saved in tmpfs volumes, if at all.  If secrets are hardcoded into Docker image variables or files, they can be inspected in a stopped container.

To run more complex queries, modify the `services.yml` file "pacs" key.  For example, the following service description would run every 10 minutes and return the accession number and patient id for any new non-con head CT's (with a matching description) from the prior 30 minutes.  See the DIANA ObservableProxiedDicom api for parameters.

```yaml
pacs:
  ctype: ObservableProxiedDicom
  proxy_desc:
    ctype: Orthanc
    host: proxy
    password: ${ORTHANC_PASSWORD}
  proxy_domain: pacs
  polling_interval: 600
  qperiod: 1800
  query: 
    "StudyDescription": "CT HEAD WO CONTRAST"
    "AccessionNumber": ""
    "PatientID": ""
```

Testing
--------

To connect directly to the Orthanc proxy (or any other DIANA service), add it in the DIANA_SERVICE variable:

`export DIANA_SERVICES='{"orthanc": {"ctype": "Orthanc","password": "${ORTHANC_PASSWORD}","host": "proxy"}}'`

- <https://codebeautify.org/yaml-to-json-xml-csv>
- <https://codebeautify.org/jsonminifier>

License
-------

MIT
