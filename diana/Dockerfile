FROM derekmerck/diana:arm32v7
VOLUME /etc/orthanc
COPY services.yml /config/diana/services.yml
WORKDIR /opt/diana
CMD bash -c 'git pull  \
    && pip install -r requirements.txt \
    && pip install -e packages/diana \
    && python3 apps/cli/diana-watcher.py -s /config/diana/services.yml -r proxied_index proxy index'