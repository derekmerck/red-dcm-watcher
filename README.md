reDIANA DICOM-Watcher
==========================

Derek Merck  
<derek_merck@brown.edu>  
Rhode Island Hospital and Brown University  
Providence, RI  

DICOM watch and report service using Orthanc and DIANA, packaged with docker-compose for Resin.io arm32v7 devices.


Use It
----------------------

```bash
$ docker-compose up
```


Configuration
--------------

Configuration is handled through the services definitions, which can be passed as a file or as an environment variable to the DIANA service.

```yaml
---

# Setup the Orthanc proxy that will be querying the PACS
proxy:

  # Host name should match service name in docker-compose
  host: dicom
  # Port is the internal port number, not the exposed port number (8999)
  port: 8042
  # Set to the new user added in "extra_users"
  user: diana
  password: xxxxxx
  # What the monitored service calls the watcher
  domains:
    pacs: DIANA

  # Should be a domain in the domains dict
  query_domain: pacs
  query_level: DicomLevel.STUDIES
  query_dict:
    'ModalitiesInStudy': ""
    'StudyDescription': ""
  # Look back over last 5 mins
  query_discovery_period: 300
  # Check for new every 2 mins (and ignore repreats)
  polling_interval: 120

  # Shared configuration dir from docker-compose
  config_fp: /etc/orthanc/orthanc.json
  # Extra modalities and users to add to the proxy service
  # Need to enter this data for the PACS to be monitored
  # Can monitor more than one PACS service using multiple routes
  extra_modalities:
    - name: pacs
      aet:  MOCK
      host: 192.168.1.115
      port: 2424
  # Overwriting the 'diana' user gets rid of the default password
  extra_users:
    - name: diana
      password: xxxxxx

# Setup the Splunk indexer
index:
  host: dev.central-imaging.com
  hec_port: 8088
  default_index: remotes
  default_token: remotes_tok
  hec_tokens:
    remotes_tok: "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"
```

License
-------

MIT
