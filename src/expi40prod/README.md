# Auth

Project user:
- adminUser
- adminP4ssword.

---

# OPC UA Server certificate

1. At first connection to OPC UA Server new certificate will be registered '+PLC-1+OPCUA-1-2+' in client,<br>
but at first will be this certificate rejected - `%CommonApplicationData%\OPC Foundation\pki\rejected`.

2. Given certificate has to be moved to trusted directory - `%CommonApplicationData%\OPC Foundation\pki\trusted`

3. OPC UA Server is configured to automatically accept client certificates. Client certificate is generated<br>
by expi40-libs/Expi40.OpcUaClient.Connect when run - no further setup needed.