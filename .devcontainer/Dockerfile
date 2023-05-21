# Specify platform, so we can use the Spark ODBC driver, which doesn't support arm64 (Apple M1)
FROM --platform=linux/amd64 mcr.microsoft.com/devcontainers/base:debian

# libsasl2-dev is required for the "sasl" python package, part of the requirements for dbt-spark
# python we need of course to run pyspark
# the rest is to support SimbaSparkODBC
RUN apt-get update && \
    apt-get -y upgrade && \
    apt-get install -y unzip && \
    apt-get install -y unixodbc-dev libsasl2-modules libsasl2-modules-gssapi-mit && \
    apt-get install -y python3-pip unzip && \
    apt-get install libsasl2-dev

# Install Spark ODBC Driver
RUN mkdir /tmp/spark_driver && \
    cd /tmp/spark_driver && \
    wget https://databricks-bi-artifacts.s3.us-east-2.amazonaws.com/simbaspark-drivers/odbc/2.6.29/SimbaSparkODBC-2.6.29.1049-Debian-64bit.zip && \
    unzip SimbaSparkODBC-2.6.29.1049-Debian-64bit.zip && \
    dpkg -i simbaspark_2.6.29.1049-2_amd64.deb && \
    echo "[ODBC Drivers]" >> /etc/odbcinst.ini && \
    echo "Simba Apache Spark ODBC Connector 64-bit=Installed" >> /etc/odbcinst.ini && \
    echo "[Simba Apache Spark ODBC Connector 64-bit]" >> /etc/odbcinst.ini && \
    echo "Description=Simba Apache Spark ODBC Connector (64-bit)" >> /etc/odbcinst.ini && \
    echo "Driver=/opt/simba/spark/lib/64/libsparkodbc_sb64.so" >> /etc/odbcinst.ini && \
    echo "[ODBC Data Sources]" >> /etc/odbc.ini && \
    echo "Simba Spark=Simba Apache Spark ODBC Connector 64-bit" >> /etc/odbc.ini && \
    echo "[Simba Spark]" >> /etc/odbc.ini && \
    echo "Driver=/opt/simba/spark/lib/64/libsparkodbc_sb64.so" >> /etc/odbc.ini && \
    cd .. && rm -rf spark_driver

# Install changie
RUN mkdir /tmp/changie && cd /tmp/changie && \
    wget https://github.com/miniscruff/changie/releases/download/v1.12.0/changie_1.12.0_linux_amd64.deb && \
    dpkg -i changie_1.12.0_linux_amd64.deb && \
    cd .. && rm -rf changie


# Install dependencies during build, will install dbt-spark in editable mode
# in the proper directory, in "postCreateCommand" of devcontainer.json
COPY . /tmp/workspace
RUN cd /tmp/workspace && make dev && rm -rf /tmp/workspace
