ARG BUILD_FROM
FROM $BUILD_FROM

# host /mnt/data/resin-data/homeassistant/hyperion --> guest 

# Add env
ENV LANG C.UTF-8 \
    HYPERION_ADDRESS=https://sourceforge.net/projects/hyperion-project/files/release \
    SERVICEL="/usr/share/hyperion/services" \
    SERVICEP="/etc/init.d" \
    BINSP=/usr/share/hyperion/bin \
	BINTP=/usr/bin

# Setup base and install dependencies
RUN apk update \
    && apk add qt qt-x11 libusb ca-certificates python2 openssh \
    # get RPI Version
    && RPI_1=`grep -m1 -c BCM2708 /proc/cpuinfo` \
    && RPI_2=`grep -m1 -c BCM2709 /proc/cpuinfo` \
    && RPI_3=`grep -m1 -c 'BCM2710\|BCM2835' /proc/cpuinfo` \
    # map config directory
    && ln -s /config/hyperion /etc/hyperion 2>/dev/null \
    #set correct Hyperion version for RPI Version
    && if [ $RPI_1 -eq 1 ]; then \
	        HYPERION_RELEASE=$HYPERION_ADDRESS/hyperion_rpi.tar.gz \
        elif [ $RPI_2 -eq 1 ]; then \
	        HYPERION_RELEASE=$HYPERION_ADDRESS/hyperion_rpi2.tar.gz \
        elif [ $RPI_3 -eq 1 ]; then \ 
	        HYPERION_RELEASE=$HYPERION_ADDRESS/hyperion_rpi3.tar.gz \
        else \
	        echo "---> Critical Error: Target platform unknown -> abort" \
	        exit 1 \
        fi

# Install
RUN wget -nv $HYPERION_RELEASE -O - | tar -C /usr/share -xz	\
    #set the executen bit (failsave) and move config to /etc/hyperion
	&& chmod +x -R $BINSP \
	# create links to the binaries
	&& ln -fs $BINSP/hyperiond $BINTP/hyperiond \
	&& ln -fs $BINSP/hyperion-remote $BINTP/hyperion-remote \
	&&ln -fs $BINSP/hyperion-v4l2 $BINTP/hyperion-v4l2 \
	&&ln -fs $BINSP/hyperion-dispmanx $BINTP/hyperion-dispmanx 2>/dev/null \
	&&ln -fs $BINSP/hyperion-x11 $BINTP/hyperion-x11 2>/dev/null \
	&& ln -fs $BINSP/hyperion-aml $BINTP/hyperion-aml 2>/dev/null \
    # place startup script in init.d [and add it to upstart (-s to respect user modified scripts)]
	#rm $SERVICEP/hyperion
	cp $SERVICEL/hyperion.init.sh $SERVICEP/hyperion 2>/dev/null ; chmod +x $SERVICEP/hyperion
	#update-rc.d hyperion defaults 98 02

# Copy data for add-on
COPY run.sh /
RUN chmod a+x /run.sh

CMD [ "/run.sh" ]


