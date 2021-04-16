.. _IntroductiontoOPC:

OPC
===

When one speaks about OPC today, one must bring to mind that by now,
this three-letter abbreviation stands for eight specifications for
servers and clients, several working groups that are working on
additional specifications and a number of technical documents and
initiatives.

The seven specifications are:

-  OPC Data Access Specification Version 3.00
-  OPC XML Data Access Specification Version 1.01
-  OPC Data eXchange Specification Version 1.00
-  OPC Alarms and Events Specification Version 1.10
-  OPC Historical Data Access Specification Version 1.20
-  OPC Batch Specification Version 2.00
-  OPC Security Specification Version 1.00
-  OPC Complex Data Specification Version 1.00

OPC

The following working groups are working on new specifications:

-  OPC Unified Architecture

The OPC Foundation provides a Compliance Test Client for the Data Access
2.05, 3.0, the XML Data Access 1.01, the Alarms and Events 1.10 and the
Historical Data Access 1.20 specifications to all its members.

The development tools which are part of the OPC Toolkit support both the
Data Access, XML Data Access and the Alarms and Events Specification.
The most important definitions of these specifications are explained
below.

DCOM
--------

OPC (OLE for Process Control) is based on Microsoft’s DCOM technology.
An explanation of design and implementation aspects for OPC is not
possible without first conveying the fundamental principles of this
technology.

Two main objectives were the catalyst for developing DCOM:

-  To be able to process documents having different information
   representation formats and
-  To ensure binary compatibility between software components (backwards
   compatibility, autonomous upgrades, co-existence of older and newer
   software).

These efforts led to the definition of the Component Object Model (COM)
by Microsoft in the first half of the last decade. The aim of this model
is to guarantee binary compatibility between software components.

Interaction between the components is based on the access to methods
grouped in interfaces. A component object contains several interfaces
encapsulating the access to the object. In the remainder of this
chapter, the term “object” is used as a synonym for “component object”.

COM defines the following:

-  the externally accessible binary structure of a COM object
-  a methodology of how an object can be queried during runtime as to
   whether it supports a certain interface or not
-  a procedure how the life cycle of objects is controlled. A reference
   counter is used for this purpose which is incremented when an
   interface is used for the first time and decremented upon release.
   The counter switches to zero if all interfaces are released. This
   indicates that the object is no longer used and allowed to terminate
   itself.
-  how interactions can be performed between objects beyond process
   boundaries. This is necessary because pointers to interfaces and
   method parameters are only valid within a process space.
-  the procedure for identifying and loading objects

DCOM (Distributed COM) has existed ever since Windows NT 4.0 came into
existence, whereby DCOM contains the following new aspects as compared
to COM:

-  distribution of resources
-  security
-  various threading models

Two important questions are: how does the client get the first pointer
to the interface of a server? How does the client launch a server?

Bild
====

The client calls the function CoGetClassObject(). This function is
provided by the operating system in the DLL ole32. The following
parameters, among others, are transferred to the function:

-  the CLSID of the server to be launched. This CLSID is unique
   world-wide and is specified by the implementing party of the server.
-  pServerInfo. This parameter obtained meaning only after the
   introduction of DCOM. This structure contains the following
   components:

   -  the name of the remote computer on which the server is to be
      launched.
   -  a structure containing authorization information.

-  identifier of an Interface IClassFactory. The identifier has been
   specified by Microsoft and is therefore always the same. The reason
   is that the same functionality is provided every time.
-  memory space for the returned pointer to the Interface IClassFactory.

The executable file containing the server is referenced by a registry
entry. The COM runtime uses this entry to start the executable.
Localization is based on the CLSID and uses entries in the registry.
When the executable is started, a factory is created and the pointer to
the interface is returned.

The client now calls the method CreateInstance on Interface
IClassFactory and the factory creates the server. This two-stage process
enables the start of servers to be adapted to different requirements.
The requested interface pointer is returned as a result of the server
creation. Now different methods can be called, e.g. QueryInterface or
other methods.

Three versions of an OPC Server can be implemented.

-  DLL/ InProc-Server:

   In this case, it operates in the process space of the OPC Client.
   This is entirely transparent for the OPC Client since COM loads the
   DLL (and unloads it). Particularities are to be taken into account
   when remotely accessing the DLL.

-  Local and remote server(OutProc Server):

   The OPC Server operates as an autonomous program. It is started from
   the OPC Client whenever it is used. Most OPC Servers have been
   implemented as local or remote servers up to now. The server can be
   located either on the same computer as the client or on another
   computer.

-  Windows NT Service:

   A service is immediately started upon booting of the operating system
   and therefore also runs without user log-in. Normally, a service does
   not have a user interface. If a user interface is required it must
   run independently from the service. An OPC Server may run as service
   only under Windows XP / Windows 2000 / Windows Vista / Windows 7 and
   related server editions.

These statements apply to all COM/DCOM Servers.

Security aspects are of supreme importance in the distribution of
components. It must be guaranteed that the data exchange between OPC
Servers and OPC Clients only occurs between the components intended for
this purpose.

A client launches a process on a remote computer. It must be authorized
for this purpose. It must again be able to perform certain actions for
this process. At the same time, however, the client must also be sure
that the server only performs actions which are appropriate to the
client. The server can impersonate the client for this purpose.

The safety requirements are bi-directional.

Security can be achieved in two ways:

-  Information on security aspects is contained in the registry. This
   works for components that are either not aware of security mechanisms
   or do not use them. It can be changed using DCOMcnfg. This is known
   as declarative security.
-  Through the introduction of DCOM, a series of program-specific
   methods are available with which widely varying security features can
   be achieved. This is known as programmatic security.
   

XML, SOAP and Web Services
------------------------------

There are a great number of products which implement DCOM based OPC
Specifications. However, there are also some usage restrictions which
have to be considered during development and use of these kind of
products.

-  DCOM does not pass firewalls, i.e. direct addressing of computers
   through the firewall is not possible. However, this is precisely what
   DCOM needs to perform an internal check.

-  There are some devices and applications which provide or require data
   and which do not run on Microsoft systems. They include, for example,
   applications in the ERP or MDS areas as data consumers or Embedded
   Devices as data sources.

These restrictions are the reason that OPC foundation has undertaken the
development of the OPC XML-DA specification. This specification is no
longer based on DCOM, but on a technology independent from a specific
operating system. Since this specification is explained later, the
relevant components of this technology will be introduced below.

The eXtensible Markup Language (XML) is a flexible data description
language which is easy to comprehend and learn. Information is exchanged
by means of readable XML documents. These contain text with embedded
structuring information. An XML document consists of elements and
attributes. Elements can contain other elements. Attributes are assigned
to elements. Structuring takes place by the selection of elements and
attributes. XML defines that elements are labeled by tags which can be
defined as desired. An XML document is called well-formed if it
corresponds to the XML syntax; it is called valid if, in addition, it
corresponds to a default schema. The schema itself is also defined using
XML. Thus, a schema defines a language for structuring information in a
specific area of application. The creation of XML documents and schemas
as well as validation and processing of the files are supported by a
variety of tools. Conversion, processing and display of the documents
can be handled flexibly.

XML documents can be exchanged between applications in different ways,
e.g. on diskette, by Email, using HTTP or TCP/IP. Today, the support of
XML is guaranteed by practically all systems. Thus, even heterogeneous
systems can easily interact by exchanging XML documents.

The Simple Object Access Protocol (SOAP) is an interaction protocol that
links two technologies: XML and HTTP. HTTP is used as the transport
protocol. The parameters of the interactions are described with XML.
SOAP is thus predestined specifically for the Internet. SOAP is a
protocol independent of object architectures (DCOM, CORBA). A SOAP
telegram consists of a part describing the structure of the HTTP call
(request/response, host, content type and content length). This part is
included in all the HTTP telegrams. A UniversalResourceIdentifier (URI)
was added, which defines the end point and the method to be called. The
method parameters are transferred as XML. The programmer is responsible
for mapping the SOAP protocol to a concrete implementation. In the
meantime, SOAP has been submitted to the W3C (World Wide Web Consortium)
for standardization. In this context the name has changed to XML
protocol. ### soap.msg

::

   POST /StockQuote HTTP/1.1
   Host: www.stockquoteserver.com
   Content-Type: text/xml; charset="utf-8"
   Content-Length: nnnn
   SOAPAction: "http://www.stockquoteserver.com/QuoteCalc#GetLastTradePrice"

   <SOAP-ENV:Envelope
     xmnls:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/"
     SOAP-ENV:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/" />
     <SOAP-ENV:Header>
         <t:Transaction
             xmlns:t="some-URI"
             SOAP-ENV:mustUnderstand="1">
                 5
             </t:Transaction>
     </SOAP-ENV:Header>
     <SOAP-ENV:Body>
         <m:GetLastTradePrice xmlns:m="Some-URI">
             <symbol>DEF</symbol>
         </m:GetLastTradePrice>
     </SOAP-ENV:Body>
   </SOAP-ENV:Envelope>

The code above shows a SOAP request sent with HTTP-Post. The first lines
are part of the HTTP header. An XML document follows. The request is
addressed to the host www.stockquoteserver.com. There, the method
GetLastTradePrice is to be called in the program QuoteCalc.

The SOAP message has a header and a body. The header attributes describe
how the request is to be processed. The body contains the method
parameter; in this case, the symbol of the company whose stock price is
to be queried.

Based on the technologies introduced above, it is already possible to
implement distributed applications which interact via SOAP and are
independent from the operating system and the hardware. However,
something is still missing: a way of describing an application‘s
interface and of generating program components from this description
which are, on the one hand, compliant with the existing infrastructure
(HTTP etc.) and which, on the other hand, can be integrated in existing
programs. This is where Web Services come into play.

The programmatic interfaces made available are referred to as Web
Services. SOAP is used as the interaction protocol between components.

Web Services are described using XML. The language used is WSDL (Web
Services Description Language), which is standardized in the W3C. One
schema defines the syntax and semantics of WSDL; based on this
definition, schemas for concrete interfaces are defined. This can be
done by individual manufacturers or by an organization, such as the OPC
Foundation. An application interacting with the Web Service will deliver
valid XML message which are compliant with the schema. The function call
is sent as a XML message. This happens also with the response and a
possible error information. Components that support or use Web Services
can be implemented on any platform supporting XML.

The introduced technologies (XML, SOAP und Web Services) were not
defined by individual companies or company groups, but by the WWW
Consortium (W3C).

This fact is also of importance for future OPC specifications. In the
past, the fact that OPC is only based on DCOM has been critisized. This
point should go away if specifications are based on XML and Web
Services.

The following table shows the current (February 2004) status of the
different specifications. Recommendation stands for agreed standard,
Draft stands for a standard which is still not agreed. Note stands for a
rather detailed working paper.

========================= ==================
XML Specifications        Release state
========================= ==================
XML 1.0                   W3C Recommendation
XML Schema Part 1 & 2 1.0 W3C Recommendation
SOAP/XMLP 1.2             W3C Recommendation
WSDL 2.0                  W3C Draft
========================= ==================

Bild
====

The figure shows the infrastructure used by OPC XML DA applications. The
concrete Web Service is defined within the OPC XML DA Specification and
described with WSDL. SOAP is used as the interaction protocol. The
different systems must support the transmission and reception of calls.
These parts of the software are derived from the WSDL description.
TCP/IP can be used as a transport protocol under HTTP which can, in
turn, run on various network protocols.

OPC Data Access
-------------------

First, an “object hierarchy” is defined in the specification. The
software component “OPC Data Access Server” contains an OPCServer object
for every client, one or more OPCGroup objects and one or more OPCItem
objects. The latter represent a data item of interest. OPCGroup objects
are logical containers for OPCItem objects. The grouping can be based on
different aspects (for example, the dynamics of the changed value). The
hierarchy is shown in the figure.

Bild
====

The software component OPC Client accesses the objects of the Data
Access Server. There is an option of building callbacks to OPC Clients
from OPCGroup objects. Data is sent from the Data Access Server to the
OPC Client via this connection upon occurrence of certain conditions.
Parameters are sent to the client in the callback allowing it to relate
the answer to a previous method request.

By following these provisions, a standardized view of all implemented
Data Access Servers from all implemented OPC Clients is guaranteed.

Another definition issue of OPC is the specification of data formats for
the variables to be transferred. # Bild

These data formats contain the value of the process variable, whose type
can be one of the usual Win32 data types and the data types derived from
them. Furthermore, a time stamp is part of the format. It contains 8
bytes and specifies the time since 01 January 1600, with a precision of
100 ns. A client, of course, only displays the useful part of this
information.

Status information on the measured value and the sensing device is
transferred in the last part. The part contains 16 bits; only 8 bits are
currently used. The field “Quality” describes whether the data value is
“valid”, “invalid” or “uncertain”. Corresponding supplemental
information is contained in the field “Status”. In this way, it is
possible to encode that a communication error exists if the value is
“invalid”.

The field “Limit” describes whether the value has exceeded the
predetermined limit value in any direction. This limit value has no
relation to the value to be transferred when an OPCGroup object is
added!

Further stipulations in the Data Access Specification pertain to the
description of errors and the definition of corresponding error codes.
For example, there is an error code in case an OPCItem object is to be
created with an invalid ItemID (OPC_E_UNKNOWNITEMID, 0xC0040007L) in the
Data Access Server. The definition of errors is based on the
requirements of DCOM, i.e. reserved areas for HRESULT are used. Thus, it
is also possible for Data Access Server manufacturers to define their
own error codes and the OPC Client can query the error string in the
Data Access Server using the corresponding method (GetErrorString). This
example of combining proprietary solutions with defined procedures
illustrates the flexibility of OPC.

Various data acquisition methods are specified in the Data Access
Specification. The functionality must be provided by every Data Access
Server. Values can be read or written synchronously or asynchronously.

With synchronous data access, the method runs to completion before it
returns.

In case of asynchronous reading or writing, the OPC Client is informed
accordingly by the Data Access Server. Asynchronous read operations can
refer to a server-internal cache (only in version 1.0A) or to the
device. Values are communicated via the callback created.

A refresh call forces a callback to the connection point sink for all
active items in the group.

Another manner of reading is that the OPC Client establishes a
connection to an OPCGroup object of the Data Access Server
(ConnectionPoint / Advise) and the latter automatically transfers data
when the corresponding conditions have been established. In this case,
the data is read from the device and written to the cache and the
condition is calculated. The frequency of the read calls is controlled
by an OPCGroup parameter.

OPC Alarms and Events
-------------------------

Alarm and Event Servers are used for reporting alarms and events to
operator stations, logger components or management subsystems.

The underlying model describes a condition as a definable state of the
Alarm and Event Server or one of its objects (for example, a process
variable). Alarms are abnormal conditions. An event is a detectable
occurrence of significance to the Alarm and Event Server. The type and
manner in which events and conditions are defined is the responsibility
of the server manufacturer and is not prescribed. However, the
attributes of a condition are defined. The following event types exist:

-  condition-related events, e.g. transition to a state represented by
   conditions
-  tracking-related events, e.g. representing a parameter change
   performed by an operator
-  simple events, e.g. device failure.

The client is informed about the occurrence of an event via callbacks
from OPCEventSubscription Objects. Events and conditions can be
structured within organization units. Filters and acknowledgement
mechanisms also exist.

Condition-related events represent a state change. States are described
by conditions. Different conditions can denote different ranges of a
measured value. In this case, the specification introduces
multiple-state conditions. Subconditions are used to represent the
substates. On the other hand, events can denote the state of the
condition itself, e.g. an incoming acknowledgement can fire an event.
Condition-related events can be related to OPCItem objects. The
definition of conditions is a task done during configuration of the
server.

Simple and tracking-related events represent changes that are not
related to measured values and do not have to be acknowledged. A device
failure is an example.

The figure illustrates the object hierarchy of an Alarm and Event
Server.

Bild
====

The top-level object is the OPCEventServer object. It provides methods
for creating the OPCServerAreaBrowser object and the
OPCEventSubscription objects.

The object OPCServerAreaBrowser provides a way for clients to browse the
event area organization implemented by the server. The implementation of
this object is optional.

OPCEventSubscription objects are used to manage events and to signal
them to clients. OPCEventSubscription objects call methods in the advise
sink on the client side.

After the OPCEventSubscription object has been added, the client can
filter events.

.. _bild-1:

Bild
====

The figure shows which mandatory attribute values are returned in the
event notification. The parameters present for simple events are also
present for condition- and tracking-related events.

The parameter Source returns the source name of the event notification.
The parameter Time contains the time of the event occurrence. The value
of the parameter Type returns the event type; possible values are
OPC_CONDITION_EVENT, OPC_SIMPLE_EVENT, and OPC_TRACKING_EVENT.
Furthermore, the event category, the severity and a message can be sent
to the client.

The following parameter is available only for tracking-related events:
ActorId: the parameter denotes the client responsible for the event.

The following parameters are present only for condition-related events:

-  ConditionName: the name of the condition related to this
   notification.
-  SubConditionName: the name of the current subcondition related to
   this notification. For a single-state condition, it contains the name
   of this condition.
-  ChangeMask: indicates to the clients which properties of the
   condition have changed to have caused the event
   (OPC_CHANGE_ACTIVE_STATE).
-  NewState: this parameter carries the current state of the condition
   (OPC_CONDITION_ACTIVE, OPC_CONDITION_ENABLED, OPC_CONDITION_ACKED).
-  ConditionQuality: quality associated with the condition state. An
   event is also fired when the quality changes.
-  AckRequired: this flag indicates that the condition requires
   acknowledgment of this event.
-  ActiveTime: time that the condition became active or the time of
   transition into the current subcondition.
-  Cookie: server-defined cookie associated with the event notification
   and used by the client during acknowledgement.
-  AckId: used only for condition-related events which are
   acknowledgement notifications. Parameter contains the AcknowledgerId.

OPC XML Data Access
-----------------------

Work on OPC XML DA started in the year 2000 with the aim to integrate
Microsoft’s XML activities into OPC. This objective has been extended to
allow the use of the specification independently of the computer
platform and computer location.

OPC XML DA products can be used both across an intranet and the
Internet. At the same time, implementations for different environments
are possible. The prerequisite is that HTTP and XML support is
available.

In view of these requirements, it soon became clear that the model
devised by the OPC DCOM DA specification could not be adopted
one-to-one.

-  An object hierarchy (OPCServer, OPCGroup, OPCItem) was defined in OPC
   DCOM DA. In a heterogeneous world, one cannot assume that the
   corresponding object models can be implemented in all computers. This
   is also true for launching and terminating servers as well as for
   finding them. OPC DCOM provides DCOM-specific solutions to these
   tasks or uses DCOM directly. With OPC XML, different, generally
   applicable solutions need to be found.

-  Transmitting data via XML is less efficient than transmitting binary
   data, as permitted by DCOM. Therefore, the client must be allowed to
   choose between the amount of information and the transmission
   performance. The OPC DCOM DA interface features a multitude of
   methods. To enhance efficiency, it is useful to combine multiple
   methods.

-  OPC DCOM DA defines callbacks. This definition, too, is subject to
   prerequisites which do not necessarily exist in that way in the
   surroundings of the Web services. HTTP is a stateless protocol. The
   server has no permanent knowledge of the client. Even though HTTP
   extensions which allow saving the corresponding knowledge do exist,
   these extensions in HTTP might not always be supported. As explained
   above, it cannot be assumed that the servers (and clients) always
   implement object models. Hence, the end points for callbacks might
   not exist, either.

-  The DCOM-based OPC world provides the server enumerator which a
   client may use to get an overall view of the existing OPC servers. A
   similar infrastructure is not (yet) available for finding OPC XML DA
   services.

The following sections deal with the methods listed in the following
table.

========================= ==========================================
Function                  Description
========================= ==========================================
GetStatus                 Get the server status
Browse                    Browse the name space
GetProperties             Get values of properties
Read                      Read data
Write                     Write data
Subscribe                 Create a subscription
SubscriptionCancel        Remove a subscription
SubscriptionPolledRefresh Get the changed values of the subscription
========================= ==========================================

The GetStatus call can be used to check whether the OPC XML DA service
is available. When the client receives a corresponding response, it
knows that the service is available. The client can also use this call
to query information about the service.

An OPC XML DA service also contains the name spaces of all variables to
which the client has read and/or write access. The majority of methods
from OPC DCOM DA for browsing the name space have been combined into one
method. A call for a client browse process is available. The service
responds by a BrowseResponse. The client can set filters, the maximum
number of items to be returned and, in the case of repeated calls, the
points for starting and resuming browsing.

By calling GetProperties, the client can query values for the
properties. The BrowseResponse returns the information on the existing
properties to the client.

After the client has browsed the name space, it can read and write
values. It directly passes the complete ItemIdentifier for the required
variable.

By setting MaxAge, the client specifies, in the read request, a limit on
a value’s age. The mode of functioning is comparable with the difference
between CACHE read and DEVICE read. If the value is sufficiently
up-to-date, the service will return a stored value. If the value is
older than MaxAge, it will request a new value and pass it to the
client. A service which persistently stores values without using calls
or stores values independently of calls, will always refresh the value.

The behaviour when writing data can be compared with the behaviour when
reading. The client passes the complete ItemIdentifier and the value to
be written. The service transmits the value and returns the result to
the client. Besides the value, the client can also write the time stamp
and the quality information and read back the value.

The specification also provides for the possibility of subscription. Web
services do not support callbacks. For this reason, a different method
had to be found.

To set up a subscription, the client transmits a request. The request
specifies the variables which the client is interested in. Values can be
specified for RequestedSamplingRate, Deadband and EnablingBuffering, for
example. RequestedSamplingRate corresponds to the UpdateRate of OPC DCOM
DA. The EnableBuffering parameter has the following meaning: The client
can retrieve the values of the callback less often than possible with
RequestedSamplingRate. This means that the server acquires values more
often than the client retrieves them. If the newly acquired values in
the server change and EnableBuffering=TRUE, the server will save the
values and pass them to the client with the next call.

The SubscriptionPingRate is a timeout defined by the client. This
timeout is monitored by the service. If the client does not perform the
calls for retrieving the values faster than the SubscriptionPingRate,
the server will cancel the subscription. With the callback rate, the
client informs the server of the frequency at which the client will
retrieve the acquired values.

The service responds by a SubscriptionResponse containing the handle for
the callback and the supported callback rate as well as the available
item values.

A subscription is canceled by the client with SubscriptionCancelRequest.
The service responds by SubscriptionCancelResponse.

Retrieving the data for a subscription is initiated by the client. It
calls SubscriptionPolledRefresh and receives the data through
SubscriptionPolledRefreshResponse. In the request, the client can tell
the service how long it will wait for the response and thus determine
that the connection will be open for a specific period of time. The
sequence is illustrated in Fig. 24. Presetting the values for HoldTime
and WaitTime allows reproducing the behavior during the automatic
transmission of the values from the server to the client. The service
waits for at least the HoldTime before sending the response. If the
values do not change during HoldTime, the service will continue to wait.
This is where WaitTime is taken into account. In this case, however, the
service transmits the response immediately to the client when a change
is detected. That closes the cycle. If no change occurs during WaitTime,
either, an empty response is transmitted on expiration of this period of
time.

Integration into Apache and Microsoft IIS
---------------------------------------------

An OPC XML DA Server, written with the OPC Toolkit C++ XML DA Server,
can be accessed via Apache or Microsoft IIS web servers or directly
without an additional web server. The integration of the OPC XML DA
Server into a web server is useful, in the following cases:

-  Besides OPC XML DA, other resources need to be accessed (e.g. HTML
   pages) over HTTP.

-  HTTPS should be used to access any OPC XML DA-enabled web server.

   However, the OPC Toolkit HTTP server does not support HTTPS. By using
   the Apache or IIS servers, HTTPS-based access can be introduced.

Apache Integration

The Apache web server can be configured so that it forwards HTTP
requests for one URL to another.

It includes the module mod_proxy. This must be configured using the
configuration file, /etc/httpd/conf/httpd.conf.

In order to configure Apache to forward any requests to an OPC XML DA
server, the following lines should be added to httpd.conf:

::

   <IfModule mod_proxy.c>
     ProxyPass localPath forwardToURL
   </IfModule>

If local access to the installed OPC Toolkit Demo server using the
Apache URL http://apache-server/proxy/local is desired, the following
should be configured:

::

   <IfModule mod_proxy.c>
     ProxyPass /proxy/local http://localhost:8080/OPC/DA
   </IfModule>

Forwarding could also be directed to another server machine. It is
possible to insert more than one ProxyPass statement to the httpd.conf
file to achieve this.

It may be necessary to adjust the ProxyTimeout value. This is only
required if the default value of 300 is not sufficient.

In addition, if the KeepAlive Off setting is present, it should be
commented out, since it setting prevents HTTP connections from being
automatically closed after each request.

To activate the changed configuration, the following command should be
run:

::

   kill –SIGHUP `cat /var/run/httpd.pid`

Microsoft IIS Integration

For the integration into the Microsoft IIS Web server, Softing has
implemented an ISAPI Extension DLL, which performs the forwarding of the
HTTP requests.

In order to install the Softing IIS Proxy DLL, the following procedure
should be followed:

-  Open the IIS configuration GUI (Settings -> Control Panel ->
   Administrative Tools -> Internet Information Services);
-  Select “Default Web Site”, right-click on it and from the resulting
   menu, select Properties;
-  In the dialog “Default Web Site Properties”, select the tab “Home
   Directory”.

Bild
====

In this dialog box, the “Execute Permissions” should be set to “Scripts
only”. For “Application Protection”, select “Medium (Pooled)” or “Low
(IIS Process)”. The latter setting increases the performance of the
ISAPI extension, but reduces security levels of the web server. The
button “Unload” releases all loaded ISAPI extension DLLs.

Click on the “Configuration…” button. This opens the following dialog: #
Bild

Select “Cache ISAPI applications”. The Softing IIS Proxy DLL caches the
connections internally to improve performance.

Clicking on the button “Add” opens the following dialog box: # Bild

In this dialog, an association between the path to the IISProxy.dll and
an arbitrary extention is created, in this case .redir. The OPC Toolkit
Demo Server installation installs the IISProxy.dll to
“:raw-latex:`\Softing`:raw-latex:`\OPC`”. The OPC Toolkit C++
installation installs the file to “<OPC Toolkit
C++>:raw-latex:`\bin`:raw-latex:`\IISProxy`”.

At “Verbs”, you can select “All Verbs” or “Limit to:” POST, depending on
the HTTP methods the IIS proxy should forward. For XML-DA OPC
applications, the POST verb is sufficient. The setting “Script engine”
indicates that the .redir file has to be included in a directory with
“Script Execute” permissions. If “Check that file exists” is selected,
it is not possible to add any file besides one with a .redir filename
extension.

This concludes all required IIS configurations.

It is now necessary to create the desired redirections. Any text editor
may be used to create the required .redir files in the
:raw-latex:`\wwwroot `directory.

A .redir file has the following syntax:

::

   http://{targethost}{:port}/{pfad}  {active-timeout}  {idle-timeout}

The timeouts are specified in seconds and are optional. The default
values of the proxy are 60 and 10.

Example: The file wwwroot:raw-latex:`\proxy`:raw-latex:`\local`.redir
with the contents http://localhost:8080/OPC/DA

This redirects all HTTP requests from
http://iis-server/proxy/local.redir to http://localhost:8080/OPC/DA.

The redirection could point to another machine. It is possible to add
more than one redirection.

The access rights of the .redir files should include read access for the
anonymous IIS user.