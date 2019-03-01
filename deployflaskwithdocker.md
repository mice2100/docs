# Use gunicorn to serve

# Use runit to start service in docker container
* normally docker containers like debian/alpine does not have many system service
* so after installed runit, it can't be configed to start automatically.
* Since runit must have PID=1(first one to start), you have to find a way to start it manually:
** Under debian, using following in Dockerfile
ENTRYPOINT ["/sbin/runit"]      -- or CMD ["/sbin/runit"]
** under alpine, use following
ENTRYPOINT ["/sbin/runsvdir", "-P", "/etc/service"]

* Create folder /etc/sv/gunicorn
* Create file /etc/sv/gunicorn/run
    #!/bin/sh

    GUNICORN=/usr/local/bin/gunicorn
    ROOT=/app
    PID=/var/run/gunicorn.pid

    APP=main:app

    if [ -f $PID ]; then rm $PID; fi

    cd $ROOT
    exec $GUNICORN -b 0.0.0.0:5000 --pid=$PID $APP

* chmod +x run

* ln -s /etc/sv/gunicorn /etc/service/gunicorn
The service will start right after this link started.
* To control this service, use "sv u/d gunicorn"
