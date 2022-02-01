# Mule FTP connector to read CSV files 

## FTP -File Transfer Protocol
**FTP** serves as an standard guide or network protocol for file transfer between several connected systems to a network TCP-Transmition Control Protocol, based on a client-server architecture, which has:
- The ability for a client to connect to a server to download files from it or send files to it, no matter the operating system.
- It offers high transfer speeds, but is not secure, since data (usernames, passwords, and more data) exchange is done on plain text, without any encryption, and be vulnerable to attackers, for it is recommended the usage of SCP and SCFTP included on SSH package.
- Usually uses port 20 and 21.

## FTP & Mule connectors
First will need an FTP server to upload a file and make them available within the server for the Mule application to consume from it as the client.

Requierements:
1. Instalation of <a src="https://filezilla-project.org/download.php?type=server">FileZilla Server</a> - as an FTP server.
    ``port: 14148`
    - **Add a user**, *Server -> Configure -> Users -> Add*
        - `ftp_test_user` is the name for the testing user for the mule application to access server files.
        - Set Credentials to:
        ```
        option: Require a password to log in.
        password: ftp_test_user 
        ```
    - **Add 2 dictories to access from server**, `emp` & `ftp-home`, those directories will be the storage area for the FTP Mule application to read and write data, are located at: `./ftp-files/emp` & `./ftp-files/ftp-home`
        - Add 2 mount points for FileZilla Server configuration test user directories: 
            -  *Server -> Configure -> Users -> ftp_test_user ->Mount points -> Add*
            1. Set Virtual path to `/empdir`, and Native Path to `./ftp-files/emp`.
                - Add a `CSV` file named `employee.csv`, with the following data:
                ```csv
                id,name,status
                111,John,A
                222,Mark,A
                333,Tom,a
                112,John,A
                223,Mark,A
                334,Tom,a

                ```
            2. Set Virtual Path `/`, and Native Path to `./ftp-files/emp`.
2. Read of CSV from FTP server to insert database.
    - Creation of a Mule project
----
## Inside the Mule application
![](https://docs.mulesoft.com/runtime-manager/_images/cloudhub-scheduler-studio.png)*Scheduler Connector inside a flow.*

A *Scheduler connector* from the Anypoint Studio Pallette, will allow us to have an asynchronous flow to execute it based on the time.

### Mule connectors

**Scheduler**
- For the Mule Application will need an *Scheduler* connector to read every working day of the month and create the salaries of an employees database.
```xml
<scheduler doc:name="Scheduler" doc:id="c3005057-d5e0-411b-8adf-bcf314e1c859" >
    <scheduling-strategy >
        <fixed-frequency frequency="2" timeUnit="MINUTES"/>
    </scheduling-strategy>
</scheduler>
```
- Scheduling Strategy
    ```
    Frecuency: 2
    Start delay: 0
    Time unit Minutes
    ```

![](https://docs.mulesoft.com/sftp-connector/1.4/_images/sftp-read-operation.png) *FTP connector for Read files operation*

**FTP - Read**
```xml
<ftp:read doc:id="18db6298-b2e8-4e50-a552-8eb58c96e56e" config-ref="FTP_Config" path="employee.csv"/>
```
- Connector configuration:
    ```
    working direcotory: empdir
    host: localhost
    port: 21
    username: ftp_test_user
    password: ftp_test_user
    ```
- File Path:
    `employee.csv`

*Note: Logging the output on the console we achieve every 2 minutes 5 automatic reads of the `employee.csv` file records while the Mule application is running:*
```csv
111,John,A
222,Mark,A
333,Tom,a
112,John,A
223,Mark,A
334,Tom,a
```
