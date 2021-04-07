---
layout: default
title: CAS - CAS Protocol Specification
category: Protocols
---

<a name="headTop"></p>
<h1 spaces-before="0">
  <em x-id="3">CAS Protocol 3.0 Specification</em>
</h1>
<h1 spaces-before="0">
  <strong x-id="1">Authors, Version</strong>
</h1>

<p spaces-before="0">
  Author: Drew Mazurek
</p>

<p spaces-before="0">
  Contributors:
</p>

<ul>
  <li>
    Susan Bramhall
  </li>
  <li>
    Howard Gilbert
  </li>
  <li>
    Andy Newman
  </li>
  <li>
    Andrew Petro
  </li>
  <li>
    Robert Oschwald [CAS 3.0]
  </li>
  <li>
    Misagh Moayyed
  </li>
</ul>

<p spaces-before="0">
  Version: 3.0.3
</p>

<p spaces-before="0">
  Release Date: 2017-12-01
</p>

<p spaces-before="0">
  Copyright &copy; 2005, Yale University
</p>

<p spaces-before="0">
  Copyright &copy; 2017, Apereo, Inc.
</p>

<p spaces-before="0">

<a name="head1"></p>
<h1 spaces-before="0">
  <strong x-id="1">1. Introduction</strong>
</h1>

<p spaces-before="0">
  This is the official specification of the CAS 1.0, 2.0 and 3.0 protocols.
</p>

<p spaces-before="0">
  The Central Authentication Service (CAS) is a single-sign-on / single-sign-off protocol for the web. It permits a user to access multiple applications while providing their credentials (such as userid and password) only once to a central CAS Server application.
</p>

<p spaces-before="0">


<a name="head1.1"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">1.1. Conventions & Definitions</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119[<a href="#1">1</a>].
  </p>
  
  <ul>
    <li>
      <p spaces-before="0">
        "Client" refers to the end user and/or the web browser.
      </p>
    </li>
    <li>
      <p spaces-before="0">
        "CAS Client" refers to the software component that is integrated with a web application and interacts with the CAS server via CAS protocol.
      </p>
    </li>
    <li>
      <p spaces-before="0">
        "Server" refers to the Central Authentication Service server.
      </p>
    </li>
    <li>
      <p spaces-before="0">
        "Service" refers to the application the client is trying to access.
      </p>
    </li>
    <li>
      <p spaces-before="0">
        "Back-end service" refers to the application a service is trying to access on behalf of a client. This can also be referred to as the "target service."
      </p>
    </li>
    <li>
      <p spaces-before="0">
        "SSO" refers to Single Sign on.
      </p>
    </li>
    <li>
      <p spaces-before="0">
        "SLO" refers to Single Logout.
      </p>
    </li>
    <li>
      <p spaces-before="0">
        "&lt;LF&gt;" is a bare line feed (ASCII value 0x0a).
      </p>
    </li>
  </ul>
  
  <p spaces-before="0">

<a name="head1.2"></p> 
    
    <p spaces-before="0">
      <strong x-id="1">1.2 Reference Implementation</strong>
    </p>
<hr />
    
    <p spaces-before="0">
      The Apereo CAS Server [<a href="#8">8</a>] is the official reference implementation of the CAS Protocol Specification.
    </p>
    
    <p spaces-before="0">
      Apereo CAS Server 4.x and above supports the CAS Protocol 3.0 Specification.
    </p>
    
    <p spaces-before="0">

<a name="head2"></p>
<h1 spaces-before="0">
  <strong x-id="1">2. CAS URIs</strong>
</h1>

<p spaces-before="0">
  CAS is an HTTP[<a href="#2">2</a>],[<a href="#3">3</a>]-based protocol that requires each of its components to be accessible through specific URIs. This section will discuss each of the URIs:
</p>

<table spaces-before="0">
  <tr>
    <th>
      URI
    </th>
    
    <th>
      Description
    </th>
  </tr>
  
  <tr>
    <td>
      <code>/login</code>
    </td>
    
    <td>
      credential requestor / acceptor
    </td>
  </tr>
  
  <tr>
    <td>
      <code>/logout</code>
    </td>
    
    <td>
      destroy CAS session (logout)
    </td>
  </tr>
  
  <tr>
    <td>
      <code>/validate</code>
    </td>
    
    <td>
      service ticket validation
    </td>
  </tr>
  
  <tr>
    <td>
      <code>/serviceValidate</code>
    </td>
    
    <td>
      service ticket validation [CAS 2.0]
    </td>
  </tr>
  
  <tr>
    <td>
      <code>/proxyValidate</code>
    </td>
    
    <td>
      service/proxy ticket validation [CAS 2.0]
    </td>
  </tr>
  
  <tr>
    <td>
      <code>/proxy</code>
    </td>
    
    <td>
      proxy ticket service [CAS 2.0]
    </td>
  </tr>
  
  <tr>
    <td>
      <code>/p3/serviceValidate</code>
    </td>
    
    <td>
      service ticket validation [CAS 3.0]
    </td>
  </tr>
  
  <tr>
    <td>
      <code>/p3/proxyValidate</code>
    </td>
    
    <td>
      service/proxy ticket validation [CAS 3.0]
    </td>
  </tr>
</table>

<p spaces-before="0">

<a name="head2.1"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">2.1. /login as credential requestor</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    The <code>/login</code> URI operates with two behaviors: as a credential requestor, and as a credential acceptor. It responds to credentials by acting as a credential acceptor and otherwise acts as a credential requestor.
  </p>
  
  <p spaces-before="0">
    If the client has already established a single sign-on session with CAS, the web browser presents to CAS a secure cookie containing a string identifying a ticket-granting ticket. This cookie is called the ticket-granting cookie. If the ticket-granting cookie keys to a valid ticket-granting ticket, CAS MAY issue a service ticket provided all the other conditions in this specification are met. See Section <a href="#head3.6">3.6</a> for more information on ticket-granting cookies.
  </p>
  
  <p spaces-before="0">

<a name="head2.1.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.1.1. parameters</strong>
</h3>

<p spaces-before="0">
  The following HTTP request parameters may be passed to <code>/login</code> while it is acting as a credential requestor. They are all case-sensitive, and they all MUST be handled by <code>/login</code>.
</p>

<ul>
  <li>
    <p spaces-before="0">
      <code>service</code> [OPTIONAL] - the identifier of the application the client is trying to access. In almost all cases, this will be the URL of the application. As a HTTP request parameter, this URL value MUST be URL-encoded as described in section 2.2 of RFC 3986 [<a href="#4">4</a>]. If a <code>service</code> is not specified and a single sign-on session does not yet exist, CAS SHOULD request credentials from the user to initiate a single sign-on session. If a <code>service</code> is not specified and a single sign-on session already exists, CAS SHOULD display a message notifying the client that it is already logged in.
    </p>
<blockquote spaces-before="4">
      <p spaces-before="0">
        Note: It is STRONGLY RECOMMENDED that all <code>service</code> urls be filtered via the service management tool, such that only authorized and known client applications would be able to use the CAS server. Leaving the service management tool open to allow lenient access to all applications will potentially increase the risk of service attacks and other security vulnerabilities. Furthermore, it is RECOMMENDED that only secure protocols such as <code>https</code> be allowed for client applications for further strengthen the authenticating client.
      </p>
    </blockquote>
  </li>
  
  <li>
    <p spaces-before="0">
      <code>renew</code> [OPTIONAL] - if this parameter is set, single sign-on will be bypassed. In this case, CAS will require the client to present credentials regardless of the existence of a single sign-on session with CAS. This parameter is not compatible with the <code>gateway</code> parameter. Services redirecting to the <code>/login</code> URI and login form views posting to the <code>/login</code> URI SHOULD NOT set both the <code>renew</code> and <code>gateway</code> request parameters. Behavior is undefined if both are set. It is RECOMMENDED that CAS implementations ignore the <code>gateway</code> parameter if <code>renew</code> is set. It is RECOMMENDED that when the <code>renew</code> parameter is set its value be "true".
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>gateway</code> [OPTIONAL] - if this parameter is set, CAS will not ask the client for credentials. If the client has a pre-existing single sign-on session with CAS, or if a single sign-on session can be established through non-interactive means (i.e. trust authentication), CAS MAY redirect the client to the URL specified by the <code>service</code> parameter, appending a valid service ticket. (CAS also MAY interpose an advisory page informing the client that a CAS authentication has taken place.) If the client does not have a single sign-on session with CAS, and a non-interactive authentication cannot be established, CAS MUST redirect the client to the URL specified by the <code>service</code> parameter with no "ticket" parameter appended to the URL. If the <code>service</code> parameter is not specified and <code>gateway</code> is set, the behavior of CAS is undefined. It is RECOMMENDED that in this case, CAS request credentials as if neither parameter was specified. This parameter is not compatible with the <code>renew</code> parameter. Behavior is undefined if both are set. It is RECOMMENDED that when the <code>gateway</code> parameter is set its value be "true".
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>method</code> [OPTIONAL, CAS 3.0] - The <code>method</code> to be used when sending responses. While native HTTP redirects (<code>GET</code>) may be utilized as the default method, applications that require a <code>POST</code> response can use this parameter to indicate the method type. A <code>HEADER</code> method may also be specified to indicate the CAS final response such as <code>service</code> and <code>ticket</code>should be returned in form of HTTP response headers.  It is up to the CAS server implementation to determine whether or not <code>POST</code> or <code>HEADER</code> responses are supported.
    </p>
  </li>
</ul>

<p spaces-before="0">

<a name="head2.1.2"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.1.2. URL examples of /login</strong>
</h3>

<p spaces-before="0">
  Simple login example:
</p>

<p spaces-before="0">
  <code>https://cas.example.org/cas/login?service=http%3A%2F%2Fwww.example.org%2Fservice</code>
</p>

<p spaces-before="0">
  Don't prompt for username/password:
</p>

<p spaces-before="0">
  <code>https://cas.example.org/cas/login?service=http%3A%2F%2Fwww.example.org%2Fservice&gateway=true</code>
</p>

<p spaces-before="0">
  Always prompt for username/password:
</p>

<p spaces-before="0">
  <code>https://cas.example.org/cas/login?service=http%3A%2F%2Fwww.example.org%2Fservice&renew=true</code>
</p>

<p spaces-before="0">
  Use POST responses instead of redirects:
</p>

<p spaces-before="0">
  <code>https://cas.example.org/cas/login?method=POST&service=http%3A%2F%2Fwww.example.org%2Fservice</code>
</p>

<p spaces-before="0">

<a name="head2.1.3"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.1.3. response for username/password authentication</strong>
</h3>

<p spaces-before="0">
  When <code>/login</code> behaves as a credential requestor, the response will vary depending on the type of credentials it is requesting. In most cases, CAS will respond by displaying a login screen requesting a username and password. This page MUST include a form with the parameters, "username", "password", and "lt". The form MAY also include the parameter "warn". If <code>service</code> was specified to <code>/login</code>, <code>service</code> MUST also be a parameter of the form, containing the value originally passed to <code>/login</code>. These parameters are discussed in detail in Section <a href="#head2.2.1">2.2.1</a>. The form MUST be submitted through the HTTP POST method to <code>/login</code> which will then act as a credential acceptor, discussed in Section <a href="#head2.2">2.2</a>.
</p>

<p spaces-before="0">

<a name="head2.1.4"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.1.4. response for trust authentication</strong>
</h3>

<p spaces-before="0">
  Trust authentication accommodates consideration of arbitrary aspects of the request as a basis for authentication. The appropriate user experience for trust authentication will be highly deployer-specific in consideration of local policy and of the logistics of the particular authentication mechanism implemented.
</p>

<p spaces-before="0">
  When <code>/login</code> behaves as a credential requestor for trust authentication, its behavior will be determined by the type of credentials it will be receiving. If the credentials are valid, CAS MAY transparently redirect the user to the service. Alternately, CAS MAY display a warning that credentials were presented and allow the client to confirm that it wants to use those credentials. It is RECOMMENDED that CAS implementations allow the deployer to choose the preferred behavior. If the credentials are invalid or non-existent, it is RECOMMENDED that CAS displays to the client the reason why authentication failed, and possibly present the user with alternate means of authentication (e.g. username/password authentication).
</p>

<p spaces-before="0">

<a name="head2.1.5"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.1.5. response for single sign-on authentication</strong>
</h3>

<p spaces-before="0">
  If the client has already established a single sign-on session with CAS, the client will have presented its HTTP session cookie to <code>/login</code> and behavior will be handled as in Section <a href="#head2.2.4">2.2.4</a>. However, if the <code>renew</code> parameter is set, the behavior will be handled as in Section <a href="#head2.1.3">2.1.3</a> or <a href="#head2.1.4">2.1.4</a>.
</p>

<p spaces-before="0">

<a name="head2.2"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">2.2. /login as credential acceptor</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    When a set of accepted credentials are passed to <code>/login</code>, <code>/login</code> acts as a credential acceptor and its behavior is defined in this Section.
  </p>
  
  <p spaces-before="0">

<a name="head2.2.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.2.1. parameters common to all types of authentication</strong>
</h3>

<p spaces-before="0">
  The following HTTP request parameters MAY be passed to <code>/login</code> while it is acting as a credential acceptor. They are all case-sensitive and they all MUST be handled by <code>/login</code>.
</p>

<ul>
  <li>
    <p spaces-before="0">
      <code>service</code> [OPTIONAL] - the URL of the application the client is trying to access. As a HTTP request parameter, this URL value MUST be URL-encoded as described in Section 2.2 of RFC 1738 [<a href="#4">4</a>]. CAS MUST redirect the client to this URL upon successful authentication. This is discussed in detail in Section <a href="#head2.2.4">2.2.4</a>. If the CAS Server operates in non-open mode (Service URLs allowed to use the CAS Server are registered within the CAS Server), the CAS Server MUST deny operation and print out a meaningful message if a non-authorized service URL is presented.
    </p>
<blockquote spaces-before="4">
      <p spaces-before="0">
        Note: It is STRONGLY RECOMMENDED that all <code>service</code> urls be filtered via the service management tool, such that only authorized and known client applications would be able to use the CAS server. Leaving the service management tool open to allow lenient access to all applications will potentially increase the risk of service attacks and other security vulnerabilities. Furthermore, it is RECOMMENDED that only secure protocols such as <code>https</code> be allowed for client applications for further strengthen the authenticating client.
      </p>
    </blockquote>
  </li>
  
  <li>
    <p spaces-before="0">
      <code>warn</code> [OPTIONAL] - if this parameter is set, single sign-on MUST NOT be transparent. The client MUST be prompted before being authenticated to another service.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>method</code> [OPTIONAL] - The <code>method</code> to be used when sending responses. See section <a href="#head2.1.1">2.1.1</a> for details
    </p>
  </li>
</ul>

<p spaces-before="0">

<a name="head2.2.2"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.2.2. parameters for username/password authentication</strong>
</h3>

<p spaces-before="0">
  In addition to the OPTIONAL parameters specified in Section <a href="#head2.2.1">2.2.1</a>, the following HTTP request parameters MUST be passed to <code>/login</code> while it is acting as a credential acceptor for username/password authentication. They are all case-sensitive.
</p>

<ul>
  <li>
    <p spaces-before="0">
      <code>username</code> [REQUIRED] - the username of the client that is trying to log in
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>password</code> [REQUIRED] - the password of the client that is trying to log in
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>lt</code> [OPTIONAL] - a login ticket. This is provided as part of the login form discussed in Section <a href="#head2.1.3">2.1.3</a>. The login ticket itself is discussed in Section <a href="#head3.5">3.5</a>.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>rememberMe</code> [OPTIONAL, CAS 3.0] - if this parameter is set, a Long-Term Ticket Granting Ticket might be created by the CAS server (referred to as Remember-Me support). It is subject to the CAS server configuration whether Long-Term Ticket Granting Tickets are supported or not.
    </p>
  </li>
</ul>

<blockquote spaces-before="0">
  <p spaces-before="0">
    Note: When Long-Term Ticket Granting Tickets (Remember Me) are supported by the CAS Server, security considerations MUST be taken into account. This for example includes shared computer usage. On CAS client systems it might be necessary to handle Remember-Me logins different. See Section <a href="#head4.1">4.1</a> for details.
  </p>
</blockquote>

<p spaces-before="0">

<a name="head2.2.3"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.2.3. parameters for trust authentication</strong>
</h3>

<p spaces-before="0">
  There are no REQUIRED HTTP request parameters for trust authentication. Trust authentication MAY be based on any aspect of the HTTP request.
</p>

<p spaces-before="0">

<a name="head2.2.4"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.2.4. response</strong>
</h3>

<p spaces-before="0">
  One of the following responses MUST be provided by <code>/login</code> when it is operating as a credential acceptor.
</p>

<ul>
  <li>
    <p spaces-before="0">
      successful login: redirect the client to the URL specified by the <code>service</code> parameter in a manner that will not cause the client's credentials to be forwarded to the <code>service</code>. This redirection MUST result in the client issuing a GET request to the <code>service</code>. The request MUST include a valid service ticket, passed as the HTTP request parameter, "ticket". See <a href="#head_appdx_b">Appendix B</a> for more information. If <code>service</code> was not specified, CAS MUST display a message notifying the client that it has successfully initiated a single sign-on session.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      failed login: return to <code>/login</code> as a credential requestor. It is RECOMMENDED in this case that the CAS server display an error message to the user describing why login failed (e.g. bad password, locked account, etc.), and if appropriate, provide an opportunity for the user to attempt to login again.
    </p>
  </li>
</ul>

<p spaces-before="0">

<a name="head2.3"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">2.3. /logout</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    <code>/logout</code> destroys a client's single sign-on CAS session. The ticket-granting cookie (Section <a href="#head3.6">3.6</a>) is destroyed, and subsequent requests to <code>/login</code> will not obtain service tickets until the user again presents primary credentials (and thereby establishes a new single sign-on session).
  </p>
  
  <p spaces-before="0">

<a name="head2.3.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.3.1. parameters</strong>
</h3>

<p spaces-before="0">
  The following HTTP request parameter MAY be specified to <code>/logout</code>. It is case sensitive and SHOULD be handled by <code>/logout</code>.
</p>

<ul>
  <li>
    <p spaces-before="0">
      <code>service</code> [OPTIONAL, CAS 3.0] - if a <code>service</code> parameter is specified, the browser might be automatically redirected to the URL specified by <code>service</code> after the logout was performed by the CAS server. If redirection by the CAS Server is actually performed depends on the server configuration. As a HTTP request parameter, the <code>service</code> value MUST be URL-encoded as described in Section 2.2 of RFC 1738 [<a href="#4">4</a>].
    </p>
<blockquote spaces-before="4">
      <p spaces-before="0">
        Note: It is STRONGLY RECOMMENDED that all <code>service</code> urls be filtered via the service management tool, such that only authorized and known client applications would be able to use the CAS server. Leaving the service management tool open to allow lenient access to all applications will potentially increase the risk of service attacks and other security vulnerabilities. Furthermore, it is RECOMMENDED that only secure protocols such as <code>https</code> be allowed for client applications for further strengthen the authenticating client.
      </p>
    </blockquote>
<blockquote spaces-before="4">
      <p spaces-before="0">
        Note: The <code>url</code> parameter defined in the former CAS 2.0 specification is not a valid parameter in CAS 3.0 anymore. CAS Servers MUST ignore given <code>url</code> parameters. A CAS client MAY provide the <code>service</code> parameter as described above, as this ensures the parameter is validated against the registered service URLs when operating in non-open mode. See <a href="#head2.3.2">2.3.2</a> for details.
      </p>
    </blockquote>
  </li>
</ul>

<p spaces-before="0">

<a name="head2.3.2"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.3.2. response</strong>
</h3>

<p spaces-before="0">
  [CAS 1.0, CAS 2.0] <code>/logout</code> MUST display a page stating that the user has been logged out. If the "url" request parameter is implemented, <code>/logout</code> SHOULD also provide a link to the provided URL as described in Section <a href="#head2.3.1">2.3.1</a>.
</p>

<p spaces-before="0">
  [CAS 3.0] <code>/logout</code> MUST display a page stating that the user has been logged out if no <code>service</code> parameter was provided. If a <code>service</code> request parameter with an encoded URL value is provided, the CAS server redirects to the given service URL after successful logout.
</p>

<blockquote spaces-before="0">
  <p spaces-before="0">
    Note: When CAS Server operates in non-open mode (allowed Service URLs are registered within the CAS Server), the CAS server MUST ensure that only registered [service] parameter Service URLs are accepted for redirection. The <code>url</code> parameter defined in the former CAS 2.0 specification is not a valid parameter in CAS 3.0 anymore. CAS Servers MUST ignore given <code>url</code> parameters.
  </p>
</blockquote>

<p spaces-before="0">

<a name="head2.3.3"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.3.3 Single Logout</strong>
</h3>

<p spaces-before="0">
  The CAS Server MAY support Single Logout (SLO). SLO means that the user gets logged out not only from the CAS Server, but also from all visited CAS client applications. If SLO is supported by the CAS Server, the CAS Server MUST send a HTTP POST request containing a logout XML document (see <a href="#head_appdx_c">Appendix C</a>) to all service URLs provided to CAS during this CAS session whenever a Ticket Granting Ticket is explicitly expired by the user (e.g. during logout). CAS Clients that do not support the SLO POST requests MUST ignore these requests. SLO requests MAY also be initiated by the CAS Server upon TGT idle timeout.
</p>

<p spaces-before="0">



<a name="head2.3.3.1"></p>

<h4 spaces-before="0">
  <strong x-id="1">2.3.3.1 Server behaviour</strong>
</h4>

<p spaces-before="0">
  The CAS Server SHALL ignore all errors that might occur on a Single Logout POST request to CAS Client applications service URLs. This ensures that any errors while sending the POST request do not disturb CAS Server performance and availability ("fire and forget").
</p>

<p spaces-before="0">



<a name="head2.3.3.2"></p>

<h4 spaces-before="0">
  <strong x-id="1">2.3.3.2 Client behaviour</strong>
</h4>

<p spaces-before="0">
  Handling the logout POST request data is up to the CAS client. It is RECOMMENDED to logout the user from the application identified by the service ticket id sent in the SLO POST request. If the client supports SLO POST request handling, the client SHALL return a HTTP success status code.
</p>

<p spaces-before="0">



<a name="head2.4"></p>

<h2 spaces-before="0">
  <strong x-id="1">2.4. /validate [CAS 1.0]</strong>
</h2>

<p spaces-before="0">
  <code>/validate</code> checks the validity of a service ticket. <code>/validate</code> is part of the CAS 1.0 protocol and thus does not handle proxy authentication. CAS MUST respond with a ticket validation failure response when a proxy ticket is passed to <code>/validate</code>.
</p>

<p spaces-before="0">



<a name="head2.4.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.4.1. parameters</strong>
</h3>

<p spaces-before="0">
  The following HTTP request parameters MAY be specified to <code>/validate</code>. They are case sensitive and MUST all be handled by <code>/validate</code>.
</p>

<ul>
  <li>
    <p spaces-before="0">
      <code>service</code> [REQUIRED] - the identifier of the service for which the ticket was issued, as discussed in Section 2.2.1. As a HTTP request parameter, the <code>service</code> value MUST be URL-encoded as described in Section 2.2 of RFC 1738 [<a href="#4">4</a>].
    </p>
<blockquote spaces-before="4">
      <p spaces-before="0">
        Note: It is STRONGLY RECOMMENDED that all <code>service</code> urls be filtered via the service management tool, such that only authorized and known client applications would be able to use the CAS server. Leaving the service management tool open to allow lenient access to all applications will potentially increase the risk of service attacks and other security vulnerabilities. Furthermore, it is RECOMMENDED that only secure protocols such as <code>https</code> be allowed for client applications for further strengthen the authenticating client.
      </p>
    </blockquote>
  </li>
  
  <li>
    <p spaces-before="0">
      <code>ticket</code> [REQUIRED] - the service ticket issued by <code>/login</code>. Service tickets are described in Section 3.1.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>renew</code> [OPTIONAL] - if this parameter is set, ticket validation will only succeed if the service ticket was issued from the presentation of the user's primary credentials. It will fail if the ticket was issued from a single sign-on session.
    </p>
  </li>
</ul>

<p spaces-before="0">



<a name="head2.4.2"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.4.2. response</strong>
</h3>

<p spaces-before="0">
  <code>/validate</code> will return one of the following two responses:
</p>

<p spaces-before="0">
  On ticket validation success:
</p>

<p spaces-before="0">
  yes&lt;LF&gt;username&lt;LF&gt;
</p>

<p spaces-before="0">
  On ticket validation failure:
</p>

<p spaces-before="0">
  no&lt;LF&gt;
</p>

<p spaces-before="0">



<a name="head2.4.3"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.4.3. URL examples of /validate</strong>
</h3>

<p spaces-before="0">
  Simple validation attempt:
</p>

<p spaces-before="0">
  <code>https://cas.example.org/cas/validate?service=http%3A%2F%2Fwww.example.org%2Fservice&ticket=ST-1856339-aA5Yuvrxzpv8Tau1cYQ7</code>
</p>

<p spaces-before="0">
  Ensure service ticket was issued by presentation of primary credentials:
</p>

<p spaces-before="0">
  <code>https://cas.example.org/cas/validate?service=http%3A%2F%2Fwww.example.org%2Fservice&ticket=ST-1856339-aA5Yuvrxzpv8Tau1cYQ7&renew=true</code>
</p>

<p spaces-before="0">



<a name="head2.5"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">2.5. /serviceValidate [CAS 2.0]</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    <code>/serviceValidate</code> checks the validity of a service ticket and returns an XML-fragment response. <code>/serviceValidate</code> MUST also generate and issue proxy-granting tickets when requested. <code>/serviceValidate</code> MUST NOT return a successful authentication if it receives a proxy ticket. It is RECOMMENDED that if <code>/serviceValidate</code> receives a proxy ticket, the error message in the XML response SHOULD explain that validation failed because a proxy ticket was passed to <code>/serviceValidate</code>.
  </p>
  
  <p spaces-before="0">



<a name="head2.5.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.5.1. parameters</strong>
</h3>

<p spaces-before="0">
  The following HTTP request parameters MAY be specified to <code>/serviceValidate</code>. They are case sensitive and MUST all be handled by <code>/serviceValidate</code>.
</p>

<ul>
  <li>
    <p spaces-before="0">
      <code>service</code> [REQUIRED] - the identifier of the service for which the ticket was issued, as discussed in Section <a href="#head2.2.1">2.2.1</a>. As a HTTP request parameter, the <code>service</code> value MUST be URL-encoded as described in Section 2.2 of RFC 1738 [<a href="#4">4</a>].
    </p>
<blockquote spaces-before="4">
      <p spaces-before="0">
        Note: It is STRONGLY RECOMMENDED that all <code>service</code> urls be filtered via the service management tool, such that only authorized and known client applications would be able to use the CAS server. Leaving the service management tool open to allow lenient access to all applications will potentially increase the risk of service attacks and other security vulnerabilities. Furthermore, it is RECOMMENDED that only secure protocols such as <code>https</code> be allowed for client applications for further strengthen the authenticating client.
      </p>
    </blockquote>
  </li>
  
  <li>
    <p spaces-before="0">
      <code>ticket</code> [REQUIRED] - the service ticket issued by <code>/login</code>. Service tickets are described in Section <a href="#head3.1">3.1</a>.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>pgtUrl</code> [OPTIONAL] - the URL of the proxy callback. Discussed in Section <a href="#head2.5.4">2.5.4</a>. As a HTTP request parameter, the "pgtUrl" value MUST be URL-encoded as described in Section 2.2 of RFC 1738 [<a href="#4">4</a>].
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>renew</code> [OPTIONAL] - if this parameter is set, ticket validation will only succeed if the service ticket was issued from the presentation of the user's primary credentials. It will fail if the ticket was issued from a single sign-on session.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>format</code> [OPTIONAL] - if this parameter is set, ticket validation response MUST be produced based on the parameter value. Supported values are <code>XML</code> and <code>JSON</code>. If this parameter is not set, the default <code>XML</code> format will be used. If the parameter value is not supported by the CAS server, an error code MUST be returned as is described in section <a href="#head2.5.3">2.5.3</a>.
    </p>
  </li>
</ul>

<p spaces-before="0">

<a name="head2.5.2"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.5.2. response</strong>
</h3>

<p spaces-before="0">
  <code>/serviceValidate</code> will return an XML-formatted CAS serviceResponse as described in the XML schema in <a href="#head_appdx_a">Appendix A</a>. Below are example responses:
</p>

<p spaces-before="0">
  <strong x-id="1">On ticket validation success:</strong>
</p>

<pre><code class="xml">&lt;cas:serviceResponse xmlns:cas="http://www.yale.edu/tp/cas"&gt;
 &lt;cas:authenticationSuccess&gt;
  &lt;cas:user&gt;username&lt;/cas:user&gt;
  &lt;cas:proxyGrantingTicket&gt;PGTIOU-84678-8a9d...&lt;/cas:proxyGrantingTicket&gt;
 &lt;/cas:authenticationSuccess&gt;
&lt;/cas:serviceResponse&gt;
</code></pre>

<pre><code class="json">{
  "serviceResponse" : {
    "authenticationSuccess" : {
      "user" : "username",
      "proxyGrantingTicket" : "PGTIOU-84678-8a9d..."
    }
  }
}
</code></pre>

<p spaces-before="0">
  <strong x-id="1">On ticket validation failure:</strong>
</p>

<pre><code class="xml">&lt;cas:serviceResponse xmlns:cas="http://www.yale.edu/tp/cas"&gt;
 &lt;cas:authenticationFailure code="INVALID_TICKET"&gt;
    Ticket ST-1856339-aA5Yuvrxzpv8Tau1cYQ7 not recognized
  &lt;/cas:authenticationFailure&gt;
&lt;/cas:serviceResponse&gt;
</code></pre>

<pre><code class="json">{
  "serviceResponse" : {
    "authenticationFailure" : {
      "code" : "INVALID_TICKET",
      "description" : "Ticket ST-1856339-aA5Yuvrxzpv8Tau1cYQ7 not recognized"
    }
  }
}
</code></pre>

<p spaces-before="0">
  For proxy responses, see section <a href="#head2.6.2">2.6.2</a>.
</p>

<p spaces-before="0">

<a name="head2.5.3"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.5.3. error codes</strong>
</h3>

<p spaces-before="0">
  The following values MAY be used as the "code" attribute of authentication failure responses. The following is the minimum set of error codes that all CAS servers MUST implement. Implementations MAY include others.
</p>

<ul>
  <li>
    <p spaces-before="0">
      <code>INVALID_REQUEST</code> - not all of the required request parameters were present
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>INVALID_TICKET_SPEC</code> - failure to meet the requirements of validation specification
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>UNAUTHORIZED_SERVICE_PROXY</code> - the service is not authorized to perform proxy authentication
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>INVALID_PROXY_CALLBACK</code> - The proxy callback specified is invalid. The credentials specified for proxy authentication do not meet the security requirements
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>INVALID_TICKET</code> - the ticket provided was not valid, or the ticket did not come from an initial login and <code>renew</code> was set on validation. The body of the <code>&lt;cas:authenticationFailure&gt;</code> block of the XML response SHOULD describe the exact details.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>INVALID_SERVICE</code> - the ticket provided was valid, but the service specified did not match the service associated with the ticket. CAS MUST invalidate the ticket and disallow future validation of that same ticket.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>INTERNAL_ERROR</code> - an internal error occurred during ticket validation
    </p>
  </li>
</ul>

<p spaces-before="0">
  For all error codes, it is RECOMMENDED that CAS provide a more detailed message as the body of the <code>&lt;cas:authenticationFailure&gt;</code> block of the XML response.
</p>

<p spaces-before="0">

<a name="head2.5.4"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.5.4. proxy callback</strong>
</h3>

<p spaces-before="0">
  If a service wishes to proxy a client's authentication to a back-end service, it must acquire a proxy-granting ticket (PGT). Acquisition of this ticket is handled through a proxy callback URL. This URL will uniquely and securely identify the service that is proxying the client's authentication. The back-end service can then decide whether or not to accept the credentials based on the proxying service identifying callback URL.
</p>

<p spaces-before="0">
  The proxy callback mechanism works as follows:
</p>

<ol start="1">
  <li>
    <p spaces-before="0">
      The service that is requesting a proxy-granting ticket (PGT) specifies upon initial service ticket or proxy ticket validation the HTTP request parameter "pgtUrl" to <code>/serviceValidate</code> (or <code>/proxyValidate</code>). This is a callback URL of the service to which CAS will connect to verify the service's identity. This URL MUST be HTTPS and CAS MUST evaluate the endpoint to establish peer trust. Building trust at a minimum involves utilizing PKIX and employing container trust to validate the signature, chain and the expiration window of the certificate of the callback url. The generation of the proxy-granting-ticket or the corresponding proxy granting ticket IOU may fail due to the proxy callback url failing to meet the minimum security requirements such as failure to establishing trust between peers or unresponsiveness of the endpoint, etc. In case of failure, no proxy-granting ticket will be issued and the CAS service response as described in Section <a href="#head2.5.2">2.5.2</a> MUST NOT contain a <code>&lt;proxyGrantingTicket&gt;</code> block. At this point, the issuance of a proxy-granting ticket is halted and service ticket validation will fail. Otherwise, the process will proceed normally to step 2.
    </p>
  </li>
  
  <li>
    <p spaces-before="0">
      CAS uses an HTTP GET request to pass the HTTP request parameters <code>pgtId</code> and <code>pgtIou</code> to the pgtUrl endpoint. These entities are discussed in Sections <a href="#head3.3">3.3</a> and <a href="#head3.4">3.4</a>, respectively. If the proxy callback url specifies any parameters, those MUST be preserved. CAS MUST also ensure that the endpoint is reachable by verifying the response HTTP status code from the GET request, as detailed in step #3. If the proxy service fails to authenticate or the endpoint responds with an unacceptable status code, proxy authentication MUST fail and CAS MUST respond with the appropriate error code as is described in section <a href="#head2.5.3">2.5.3</a>.
    </p>
  </li>
  
  <li>
    <p spaces-before="0">
      If the HTTP GET returns an HTTP status code of 200 (OK), CAS MUST respond to the <code>/serviceValidate</code> (or <code>/proxyValidate</code>) request with a service response (Section <a href="#head2.5.2">2.5.2</a>) containing the proxy-granting ticket IOU (Section <a href="#head3.4">3.4</a>) within the <code>&lt;cas:proxyGrantingTicket&gt;</code> block. If the HTTP GET returns any other status code, except HTTP 3xx redirects, CAS MUST respond to the <code>/serviceValidate</code> (or <code>/proxyValidate</code>) request with a service response that MUST NOT contain a <code>&lt;cas:proxyGrantingTicket&gt;</code> block. CAS MAY follow any HTTP redirects issued by the <code>pgtUrl</code>. However, the identifying callback URL provided upon validation in the <code>&lt;proxy&gt;</code> block MUST be the same URL that was initially passed to <code>/serviceValidate</code> (or <code>/proxyValidate</code>) as the <code>pgtUrl</code> parameter.
    </p>
  </li>
  
  <li>
    <p spaces-before="0">
      The service, having received a proxy-granting ticket IOU in the CAS response, and both a proxy-granting ticket and a proxy-granting ticket IOU from the proxy callback, will use the proxy-granting ticket IOU to correlate the proxy-granting ticket with the validation response. The service will then use the proxy-granting ticket for the acquisition of proxy tickets as described in Section <a href="#head2.7">2.7</a>.
    </p>
  </li>
</ol>

<p spaces-before="0">



<a name="head2.5.5"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.5.5. attributes [CAS 3.0]</strong>
</h3>

<p spaces-before="0">
  [CAS 3.0] The response document MAY include an optional <cas:attributes> element for additional authentication and/or user attributes. See <a href="#head_appdx_a">Appendix A</a> for details.
</p>

<p spaces-before="0">

<a name="head2.5.6"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.5.6. URL examples of /serviceValidate</strong>
</h3>

<p spaces-before="0">
  Simple validation attempt:
</p>

<p spaces-before="0">
  <code>https://cas.example.org/cas/serviceValidate?service=http%3A%2F%2Fwww.example.org%2Fservice&ticket=ST-1856339-aA5Yuvrxzpv8Tau1cYQ7</code>
</p>

<p spaces-before="0">
  Ensure service ticket was issued by presentation of primary credentials:
</p>

<p spaces-before="0">
  <code>https://cas.example.org/cas/serviceValidate?service=http%3A%2F%2Fwww.example.org%2Fservice&ticket=ST-1856339-aA5Yuvrxzpv8Tau1cYQ7&renew=true</code>
</p>

<p spaces-before="0">
  Pass in a callback URL for proxying:
</p>

<p spaces-before="0">
  <code>https://cas.example.org/cas/serviceValidate?service=http%3A%2F%2Fwww.example.org%2Fservice&ticket=ST-1856339-aA5Yuvrxzpv8Tau1cYQ7&pgtUrl=https://www.example.org%2Fservice%2FproxyCallback</code>
</p>

<p spaces-before="0">



<a name="head2.5.7"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.5.7 Example response with custom attributes</strong>
</h3>

<pre><code class="xml">  &lt;cas:serviceResponse xmlns:cas="http://www.yale.edu/tp/cas"&gt;
    &lt;cas:authenticationSuccess&gt;
      &lt;cas:user&gt;username&lt;/cas:user&gt;
      &lt;cas:attributes&gt;
        &lt;cas:firstname&gt;John&lt;/cas:firstname&gt;
        &lt;cas:lastname&gt;Doe&lt;/cas:lastname&gt;
        &lt;cas:title&gt;Mr.&lt;/cas:title&gt;
        &lt;cas:email&gt;jdoe@example.org&lt;/cas:email&gt;
        &lt;cas:affiliation&gt;staff&lt;/cas:affiliation&gt;
        &lt;cas:affiliation&gt;faculty&lt;/cas:affiliation&gt;
      &lt;/cas:attributes&gt;
      &lt;cas:proxyGrantingTicket&gt;PGTIOU-84678-8a9d...&lt;/cas:proxyGrantingTicket&gt;
    &lt;/cas:authenticationSuccess&gt;
  &lt;/cas:serviceResponse&gt;
</code></pre>

<pre><code class="json">{
  "serviceResponse" : {
    "authenticationSuccess" : {
      "user" : "username",
      "proxyGrantingTicket" : "PGTIOU-84678-8a9d...",
      "proxies" : [ "https://proxy1/pgtUrl", "https://proxy2/pgtUrl" ],
      "attributes" : {
        "firstName" : "John",
        "affiliation" : [ "staff", "faculty" ],
        "title" : "Mr.",
        "email" : "jdoe@example.orgmailto:jdoe@example.org",
        "lastname" : "Doe"
      }
    }
  }
}
</code></pre>

<p spaces-before="0">

<a name="head2.6"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">2.6. /proxyValidate [CAS 2.0]</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    <code>/proxyValidate</code> MUST perform the same validation tasks as <code>/serviceValidate</code> and additionally validate proxy tickets. <code>/proxyValidate</code> MUST be capable of validating both service tickets and proxy tickets. See Section <a href="#head2.5.4">2.5.4</a> for details.
  </p>
  
  <p spaces-before="0">



<a name="head2.6.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.6.1. parameters</strong>
</h3>

<p spaces-before="0">
  <code>/proxyValidate</code> has the same parameter requirements as <code>/serviceValidate</code>. See Section <a href="#head2.5.1">2.5.1</a>.
</p>

<p spaces-before="0">



<a name="head2.6.2"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.6.2. response</strong>
</h3>

<p spaces-before="0">
  <code>/proxyValidate</code> will return an XML-formatted CAS serviceResponse as described in the XML schema in <a href="#head_appdx_a">Appendix A</a>. Below are example responses:
</p>

<p spaces-before="0">
  Response on ticket validation success:
</p>

<pre><code class="xml">  &lt;cas:serviceResponse xmlns:cas="http://www.yale.edu/tp/cas"&gt;
    &lt;cas:authenticationSuccess&gt;
      &lt;cas:user&gt;username&lt;/cas:user&gt;
      &lt;cas:proxyGrantingTicket&gt;PGTIOU-84678-8a9d...&lt;/cas:proxyGrantingTicket&gt;
      &lt;cas:proxies&gt;
        &lt;cas:proxy&gt;https://proxy2/pgtUrl&lt;/cas:proxy&gt;
        &lt;cas:proxy&gt;https://proxy1/pgtUrl&lt;/cas:proxy&gt;
      &lt;/cas:proxies&gt;
    &lt;/cas:authenticationSuccess&gt;
  &lt;/cas:serviceResponse&gt;
</code></pre>

<pre><code class="json">{
  "serviceResponse" : {
    "authenticationSuccess" : {
      "user" : "username",
      "proxyGrantingTicket" : "PGTIOU-84678-8a9d...",
      "proxies" : [ "https://proxy1/pgtUrl", "https://proxy2/pgtUrl" ]
    }
  }
}
</code></pre>


<blockquote spaces-before="0">
  <p spaces-before="0">
    Note: when authentication has proceeded through multiple proxies, the order in which the proxies were traversed MUST be reflected in the <cas:proxies> block. The most recently-visited proxy MUST be the first proxy listed, and all the other proxies MUST be shifted down as new proxies are added. In the above example, the service identified by <a href="https://proxy1/pgtUrl" x-nc="1">https://proxy1/pgtUrl</a> was visited first, and that service proxied authentication to the service identified by <a href="https://proxy2/pgtUrl" x-nc="1">https://proxy2/pgtUrl</a>.
  </p>
</blockquote>

<p spaces-before="0">
  Response on ticket validation failure:
</p>

<pre><code class="xml">  &lt;cas:serviceResponse xmlns:cas='http://www.yale.edu/tp/cas'&gt;
      &lt;cas:authenticationFailure code="INVALID_TICKET"&gt;
         ticket PT-1856376-1HMgO86Z2ZKeByc5XdYD not recognized
      &lt;/cas:authenticationFailure&gt;
  &lt;/cas:serviceResponse&gt;
</code></pre>

<pre><code class="json">{
  "serviceResponse" : {
    "authenticationFailure" : {
      "code" : "INVALID_TICKET",
      "description" : "Ticket PT-1856339-aA5Yuvrxzpv8Tau1cYQ7 not recognized"
    }
  }
}
</code></pre>

<p spaces-before="0">

<a name="head2.6.3"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.6.3 error codes</strong>
</h3>

<p spaces-before="0">
  See section <a href="#head2.5.3">2.5.3</a>
</p>

<p spaces-before="0">



<a name="head2.6.4"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.6.4 URL examples of /proxyValidate</strong>
</h3>

<p spaces-before="0">
  <code>/proxyValidate</code> accepts the same parameters as <code>/serviceValidate</code>. See Section <a href="#head2.5.5">2.5.5</a> for use examples, substituting "proxyValidate" for "serviceValidate".
</p>

<p spaces-before="0">



<a name="head2.7"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">2.7. /proxy [CAS 2.0]</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    <code>/proxy</code> provides proxy tickets to services that have acquired proxy-granting tickets and will be proxying authentication to back-end services.
  </p>
  
  <p spaces-before="0">


<a name="head2.7.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.7.1. parameters</strong>
</h3>

<p spaces-before="0">
  The following HTTP request parameters MUST be specified to <code>/proxy</code>. They are both case-sensitive.
</p>

<ul>
  <li>
    <code>pgt</code> [REQUIRED] - the proxy-granting ticket acquired by the service during service ticket or proxy ticket validation.
  </li>
  <li>
    <code>targetService</code> [REQUIRED] - the service identifier of the back-end service. Note that not all back-end services are web services so this service identifier will not always be an URL. However, the service identifier specified here MUST match the <code>service</code> parameter specified to <code>/proxyValidate</code> upon validation of the proxy ticket.
  </li>
</ul>

<p spaces-before="0">


<a name="head2.7.2"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.7.2. response</strong>
</h3>

<p spaces-before="0">
  <code>/proxy</code> will return an XML-formatted CAS serviceResponse document as described in the XML schema in <a href="#head_appdx_a">Appendix A</a>. Below are example responses:
</p>

<p spaces-before="0">
  Response on request success:
</p>

<pre><code class="xml">  &lt;cas:serviceResponse xmlns:cas="http://www.yale.edu/tp/cas"&gt;
      &lt;cas:proxySuccess&gt;
          &lt;cas:proxyTicket&gt;PT-1856392-b98xZrQN4p90ASrw96c8&lt;/cas:proxyTicket&gt;
      &lt;/cas:proxySuccess&gt;
  &lt;/cas:serviceResponse&gt;
</code></pre>

<p spaces-before="0">
  Response on request failure:
</p>

<pre><code class="xml">&lt;cas:serviceResponse xmlns:cas="http://www.yale.edu/tp/cas"&gt;
      &lt;cas:proxyFailure code="INVALID_REQUEST"&gt;
          'pgt' and 'targetService' parameters are both required
      &lt;/cas:proxyFailure&gt;
  &lt;/cas:serviceResponse&gt;
</code></pre>

<pre><code class="json">{
  "serviceResponse" : {
    "authenticationFailure" : {
      "code" : "INVALID_REQUEST",
      "description" : "'pgt' and 'targetService' parameters are both required"
    }
  }
}
</code></pre>

<p spaces-before="0">

<a name="head2.7.3"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.7.3. error codes</strong>
</h3>

<p spaces-before="0">
  The following values MAY be used as the <code>code</code> attribute of authentication failure responses. The following is the minimum set of error codes that all CAS servers MUST implement. Implementations MAY include others.
</p>

<ul>
  <li>
    <p spaces-before="0">
      <code>INVALID_REQUEST</code> - not all of the required request parameters were present
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>UNAUTHORIZED_SERVICE</code> - service is unauthorized to perform the proxy request
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>INTERNAL_ERROR</code> - an internal error occurred during ticket validation
    </p>
  </li>
</ul>

<p spaces-before="0">
  For all error codes, it is RECOMMENDED that CAS provide a more detailed message as the body of the <cas:authenticationFailure> block of the XML response.
</p>

<p spaces-before="0">



<a name="head2.7.4"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.7.4. URL example of /proxy</strong>
</h3>

<p spaces-before="0">
  Simple proxy request:
</p>

<p spaces-before="0">
  <code>https://server/cas/proxy?targetService=http%3A%2F%2Fwww.service.com&pgt=PGT-490649-W81Y9Sa2vTM7hda7xNTkezTbVge4CUsybAr</code>
</p>

<p spaces-before="0">


<a name="head3"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.7.4 Service Ticket Lifecycle implications</strong>
</h3>

<p spaces-before="0">
  The CAS Server implementation MAY update the parent Service Ticket (ST) lifetime upon proxy ticket generation.
</p>

<p spaces-before="0">
  <strong x-id="1">2.8. /p3/serviceValidate [CAS 3.0]</strong>
</p>
<hr />

<p spaces-before="0">
  <code>/p3/serviceValidate</code> MUST perform the same validation tasks as <code>/serviceValidate</code> and additionally return user attributes in the CAS response. See Section <a href="#head2.5">2.5</a> and Section <a href="#head2.5.7">2.5.7</a> for details.
</p>

<p spaces-before="0">

<a name="head2.8.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.8.1. parameters</strong>
</h3>

<p spaces-before="0">
  <code>/p3/serviceValidate</code> has the same parameter requirements as <code>/serviceValidate</code>. See Section <a href="#head2.5.1">2.5.1</a>.
</p>

<p spaces-before="0">
  <strong x-id="1">2.9. /p3/proxyValidate [CAS 3.0]</strong>
</p>
<hr />

<p spaces-before="0">
  <code>/p3/proxyValidate</code> MUST perform the same validation tasks as <code>/p3/serviceValidate</code> and additionally validate proxy tickets. See Section <a href="#head2.5">2.8</a>.
</p>

<p spaces-before="0">

<a name="head2.8.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">2.9.1. parameters</strong>
</h3>

<p spaces-before="0">
  <code>/p3/proxyValidate</code> has the same parameter requirements as <code>/p3/serviceValidate</code>. See Section <a href="#head2.8.1">2.8.1</a>.
</p>
<h1 spaces-before="0">
  <strong x-id="1">3. CAS Entities</strong>
</h1>

<p spaces-before="0">

<a name="head3.1"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">3.1. service ticket</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    A service ticket is an opaque string that is used by the client as a credential to obtain access to a service. The service ticket is obtained from CAS upon a client's presentation of credentials and a service identifier to <code>/login</code> as described in Section <a href="#head2.2">2.2</a>.
  </p>
  
  <p spaces-before="0">

<a name="head3.1.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">3.1.1. service ticket properties</strong>
</h3>

<ul>
  <li>
    <p spaces-before="0">
      Service tickets are only valid for the service identifier that was specified to <code>/login</code> when they were generated. The service identifier SHOULD NOT be part of the service ticket.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Service tickets MUST only be valid for one ticket validation attempt. Whether or not validation was successful, CAS MUST then invalidate the ticket, causing all future validation attempts of that same ticket to fail.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      CAS SHOULD expire unvalidated service tickets in a reasonable period of time after they are issued. If a service presents an expired service ticket for validation, CAS MUST respond with a validation failure response.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      It is RECOMMENDED that the validation response include a descriptive message explaining why validation failed.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      It is RECOMMENDED that the duration a service ticket is valid before it expires be no longer than five minutes. Local security and CAS usage considerations MAY determine the optimal lifespan of unvalidated service tickets.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Service tickets MUST contain adequate secure random data so that a ticket is not guessable.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Service tickets MUST begin with the characters, <code>ST-</code>.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Services MUST be able to accept service tickets of up to 32 characters in length. It is RECOMMENDED that services support service tickets of up to 256 characters in length.
    </p>
  </li>
</ul>

<p spaces-before="0">



<a name="head3.2"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">3.2. proxy ticket</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    A proxy ticket is an opaque string that a service uses as a credential to obtain access to a back-end service on behalf of a client. Proxy tickets are obtained from CAS upon a service's presentation of a valid proxy-granting ticket (Section <a href="#head3.3">3.3</a>), and a service identifier for the back-end service to which it is connecting.
  </p>
  
  <p spaces-before="0">



<a name="head3.2.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">3.2.1. proxy ticket properties</strong>
</h3>

<ul>
  <li>
    <p spaces-before="0">
      Proxy tickets are only valid for the service identifier specified to <code>/proxy</code> when they were generated. The service identifier SHOULD NOT be part of the proxy ticket.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Proxy tickets MUST only be valid for one ticket validation attempt. Whether or not validation was successful, CAS MUST then invalidate the ticket, causing all future validation attempts of that same ticket to fail.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      CAS SHOULD expire unvalidated proxy tickets in a reasonable period of time after they are issued. If a service presents for validation an expired proxy ticket, CAS MUST respond with a validation failure response.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      It is RECOMMENDED that the validation response include a descriptive message explaining why validation failed.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      It is RECOMMENDED that the duration a proxy ticket is valid before it expires be no longer than five minutes. Local security and CAS usage considerations MAY determine the optimal lifespan of unvalidated proxy tickets.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Proxy tickets MUST contain adequate secure random data so that a ticket is not guessable.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Proxy tickets SHOULD begin with the characters, <code>PT-</code>.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Back-end services MUST be able to accept proxy tickets of up to 32 characters in length.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      It is RECOMMENDED that back-end services support proxy tickets of up to 256 characters in length.
    </p>
  </li>
</ul>

<p spaces-before="0">



<a name="head3.3"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">3.3. proxy-granting ticket</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    A proxy-granting ticket (PGT) is an opaque string that is used by a service to obtain proxy tickets for obtaining access to a back-end service on behalf of a client. Proxy-granting tickets are obtained from CAS upon validation of a service ticket or a proxy ticket. Proxy-granting ticket issuance is described fully in Section <a href="#head2.5.4">2.5.4</a>.
  </p>
  
  <p spaces-before="0">



<a name="head3.3.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">3.3.1. proxy-granting ticket properties</strong>
</h3>

<ul>
  <li>
    <p spaces-before="0">
      Proxy-granting tickets MAY be used by services to obtain multiple proxy tickets. Proxy-granting tickets are not one-time-use tickets.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Proxy-granting tickets MUST expire when the client whose authentication is being proxied logs out of CAS.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Proxy-granting tickets MUST contain adequate secure random data so that a ticket is not guessable in a reasonable period of time through brute-force attacks.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Proxy-granting tickets SHOULD begin with the characters <code>PGT-</code>.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Services MUST be able to handle proxy-granting tickets of up to 64 characters in length.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      It is RECOMMENDED that services support proxy-granting tickets of up to 256 characters in length.
    </p>
  </li>
</ul>

<p spaces-before="0">



<a name="head3.4"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">3.4. proxy-granting ticket IOU</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    A proxy-granting ticket IOU is an opaque string that is placed in the response provided by <code>/serviceValidate</code> and <code>/proxyValidate</code> used to correlate a service ticket or proxy ticket validation with a particular proxy-granting ticket. See Section <a href="#head2.5.4">2.5.4</a> for a full description of this process.
  </p>
  
  <p spaces-before="0">



<a name="head3.4.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">3.4.1. proxy-granting ticket IOU properties</strong>
</h3>

<ul>
  <li>
    <p spaces-before="0">
      Proxy-granting ticket IOUs SHOULD NOT contain any reference to their associated proxy-granting tickets. Given a particular PGTIOU, it MUST NOT be possible to derive its corresponding PGT through algorithmic methods in a reasonable period of time.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Proxy-granting ticket IOUs MUST contain adequate secure random data so that a ticket is not guessable in a reasonable period of time through brute-force attacks.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Proxy-granting ticket IOUs SHOULD begin with the characters, <code>PGTIOU-</code>.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Services MUST be able to handle PGTIOUs of up to 64 characters in length. It is RECOMMENDED that services support PGTIOUs of up to 256 characters in length.
    </p>
  </li>
</ul>

<p spaces-before="0">



<a name="head3.5"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">3.5. login ticket</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    A login ticket is an <em x-id="3">optional</em> string that MAY be provided by <code>/login</code> as a credential requester and passed to <code>/login</code> as a credential acceptor for username/password authentication. Its purpose is to prevent the replaying of credentials due to bugs in web browsers.
  </p>
  
  <p spaces-before="0">


<a name="head3.5.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">3.5.1. login ticket properties</strong>
</h3>

<ul>
  <li>
    <p spaces-before="0">
      Login tickets issued by <code>/login</code> MUST be probabilistically unique.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Login tickets MUST only be valid for one authentication attempt. Whether or not authentication was successful, CAS MUST then invalidate the login ticket, causing all future authentication attempts with that instance of that login ticket to fail.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Login tickets SHOULD begin with the characters <code>LT-</code>.
    </p>
  </li>
</ul>

<p spaces-before="0">

<a name="head3.6"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">3.6. ticket-granting cookie</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    A ticket-granting cookie is an HTTP cookie[<a href="#5">5</a>] set by CAS upon the establishment of a single sign-on session. This cookie maintains login state for the client, and while it is valid, the client can present it to CAS in lieu of primary credentials. Services can opt out of single sign-on through the <code>renew</code> parameter described in Sections <a href="#head2.1.1">2.1.1</a>, <a href="#head2.4.1">2.4.1</a>, and <a href="#head2.5.1">2.5.1</a>.
  </p>
  
  <p spaces-before="0">



<a name="head3.6.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">3.6.1. ticket-granting cookie properties</strong>
</h3>

<ul>
  <li>
    <p spaces-before="0">
      A ticket-granting cookie SHALL be set to expire at the end of the client's browser session if Long-Term support is not active (<a href="#head4.1.1">4.1.1</a>) for the corresponding TGT.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      CAS SHALL set the cookie path to be as restrictive as possible. For example, if the CAS server is set up under the path /cas, the cookie path SHALL be set to /cas.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      The value of ticket-granting cookies SHALL contain adequate secure random data so that a ticket-granting cookie is not guessable in a reasonable period of time.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      The name of ticket-granting cookies SHOULD begin with the characters <code>TGC-</code>.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      The value of ticket-granting cookies SHOULD follow the same rules as the ticket-granting ticket. Typically, the value of the ticket-granting cookies MAY contain the ticket-granting ticket itself as the representation of the authenticated single sign-on session.
    </p>
  </li>
</ul>

<p spaces-before="0">

<a name="head3.7"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">3.7. ticket and ticket-granting cookie character set</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    In addition to the above requirements, all CAS tickets and the value of the ticket-granting cookie MUST contain only characters from the set <code>{A-Z, a-z, 0-9}</code>, and the hyphen character <code>-</code>.
  </p>
  
  <p spaces-before="0">


<a name="head3.8"></p> 
    
    <p spaces-before="0">
      <strong x-id="1">3.8. ticket-granting ticket</strong>
    </p>
<hr />
    
    <p spaces-before="0">
      A ticket-granting ticket (TGT) is an opaque string that is generated by the CAS server that is issued upon an successful authentication event upon <code>/login</code>. This ticket may be tied to the ticket-granting cookie which represents the state of the single sign-on session, with validity period and acts as the foundation and baseline for issuance of service tickets, proxy-granting tickets, and more.
    </p>
    
    <p spaces-before="0">

<a name="head3.8.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">3.8.1. ticket-granting ticket properties</strong>
</h3>

<ul>
  <li>
    <p spaces-before="0">
      Ticket-granting tickets MAY be used by services to obtain multiple service tickets. Ticket-granting tickets are not one-time-use tickets and are associated with a validity period and expiration policy.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Ticket-granting tickets MUST expire when the client whose authentication is being managed logs out of CAS.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Ticket-granting tickets MUST contain adequate secure random data so that a ticket is not guessable in a reasonable period of time through brute-force attacks.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Ticket-granting tickets SHOULD begin with the characters <code>TGT-</code>.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      It is RECOMMENDED that ticket-granting tickets be encrypted when shared with other external resources in order to minimize security vulnerabilities as they are tied to the ticket-granting cookie and represent the authentication session.
    </p>
  </li>
</ul>

<p spaces-before="0">


<a name="head4"></p>
<h1 spaces-before="0">
  <strong x-id="1">4. Optional Features</strong>
</h1>

<p spaces-before="0">

<a name="head4.1"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">4.1 Long-Term Tickets - Remember-Me [CAS 3.0]</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    CAS Server MAY support Long-Term Ticket Granting Tickets (referred to as "Remember Me" functionality). If this feature is supported by the CAS Server, it is possible to perform recurring, non interactive re-logins to the CAS Server as long as the Long-Term Ticket Granting Ticket in the CAS Server is not expired and the browsers TGC Cookie is valid.
  </p>
  
  <p spaces-before="0">


<a name="head4.1.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">4.1.1 Enabling Remember-Me (Login Page)</strong>
</h3>

<ul>
  <li>
    <p spaces-before="0">
      The CAS Server MUST provide a checkbox on the login page to allow Remember-Me functionality.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      By default, the checkbox MUST be unchecked.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      It MUST be the users choice to enable Remember-Me for the login or not. See Section <a href="#head2.2.2">2.2.2</a>.
    </p>
  </li>
</ul>

<p spaces-before="0">



<a name="head4.1.2"></p>

<h3 spaces-before="0">
  <strong x-id="1">4.1.2 Security implications</strong>
</h3>

<p spaces-before="0">
  Enabling Remember-Me MAY have security implications. As the CAS authentication is bound to the browser and the user is not getting interactively logged-in when a valid Long-Term TGT ticket exists and the CAS cookie presented by the browser is valid, special care MUST be taken on the CAS client side to handle Remember-Me logins properly. It MUST be the CAS clients responsibility to decide if and when Remember-Me CAS logins might be handled special. See <a href="#head4.1.3">4.1.3</a>.
</p>

<p spaces-before="0">



<a name="head4.1.3"></p>

<h3 spaces-before="0">
  <strong x-id="1">4.1.3 CAS Validation Response Attributes</strong>
</h3>

<p spaces-before="0">
  As only the CAS Client MUST decide how to handle Remember-Me logins (see <a href="#head4.2.1">4.2.1</a>), the CAS Server MUST provide information about a Remember-Me login to the CAS Client. This information MUST be provided by all ticket validation methods supported by the CAS Server (See Sections <a href="#head2.5">2.5</a>, <a href="#head2.6">2.6</a> and <a href="#head2.8">2.8</a>) in this case.
</p>

<ul>
  <li>
    <p spaces-before="0">
      In serviceValidate XML responses (see <a href="#head_appdx_a">Appendix A</a>), a Remember-Me login MUST be indicated by the <code>longTermAuthenticationRequestTokenUsed</code> attribute. Additionally, the <code>isFromNewLogin</code> attribute MAY be used to decide whether this has security implications.
    </p>
  </li>
  <li>
    <p spaces-before="0">
      In SAML validation responses, Remember-Me MUST be indicated by the <code>longTermAuthenticationRequestTokenUsed</code> attribute.
    </p>
  </li>
</ul>

<p spaces-before="0">



<a name="head4.1.4"></p>

<h3 spaces-before="0">
  <strong x-id="1">4.1.4 CAS Client requirements</strong>
</h3>

<p spaces-before="0">
  If the CAS client needs to handle Remember-Me logins special (e.g. deny access to sensitive areas of the CAS client application on a remembered login), the CAS client MUST NOT use the <code>/validate</code> CAS validation URL, as this URL does not support CAS attributes in the validation response document.
</p>


<h3 spaces-before="0">
  <strong x-id="1">4.1.5 Long-Term ticket-granting cookie properties</strong>
</h3>

<p spaces-before="0">
  When a Long-Term TGT was created by the CAS Server, the Ticket-granting cookie MUST NOT expire at the end of the client's browser session as defined in <a href="#head3.6.1">3.6.1</a>. Instead, the Ticket Granting cookie SHALL expire at the defined Long-Term TGT ticket lifetime.
</p>

<p spaces-before="0">
  The lifetime value definition of Long-Term Ticket Granting Tickets is up to the CAS Server implementer. The Long-Term Ticket Granting Ticket lifetime MAY not exceed 3 months.
</p>

<p spaces-before="0">


<a name="head4.2"></p> 
  
  <p spaces-before="0">
    <strong x-id="1">4.2 /samlValidate [CAS 3.0]</strong>
  </p>
<hr />
  
  <p spaces-before="0">
    <code>/samlValidate</code> checks the validity of a Service Ticket by a SAML 1.1 request document provided by a HTTP POST. A SAML (Secure Access Markup Language)[<a href="#7">7</a>] 1.1 response document MUST be returned. This allows the release of additional information (attributes) of the authenticated NetID. The Security Assertion Markup Language (SAML) describes a document and protocol framework by which security assertions (such as assertions about a prior act of authentication) can be exchanged.
  </p>
  
  <p spaces-before="0">



<a name="head4.2.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">4.2.1 parameters</strong>
</h3>

<p spaces-before="0">
  The following HTTP request parameters MUST be specified to <code>/samlValidate</code>. They are both case-sensitive.
</p>

<ul>
  <li>
    <code>TARGET</code> [REQUIRED] - URL encoded service identifier of the back-end service. Note that as an HTTP request parameter, this URL value MUST be URL-encoded as described in Section 2.2 of RFC 1738<a href="#4">[4]</a>. The service identifier specified here MUST match the <code>service</code> parameter provided to <code>/login</code>. See Section <a href="#head2.1.1">2.1.1</a>. The <code>TARGET</code> service SHALL use HTTPS. SAML attributes MUST NOT be released to a non-SSL site.
  </li>
</ul>

<p spaces-before="0">



<a name="head4.2.2"></p>

<h3 spaces-before="0">
  <strong x-id="1">4.2.2 HTTP Request Method and Body</strong>
</h3>

<p spaces-before="0">
  Request to /samlValidate MUST be a HTTP POST request. The request body MUST be a valid SAML 1.0 or 1.1 request XML document of document type "text/xml".
</p>

<p spaces-before="0">



<a name="head4.2.3"></p>

<h3 spaces-before="0">
  <strong x-id="1">4.2.3 SAML request values</strong>
</h3>

<ul>
  <li>
    <p spaces-before="0">
      <code>RequestID</code> [REQUIRED] - unique identifier for the request
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>IssueInstant</code> [REQUIRED] - timestamp of the request
    </p>
  </li>
  <li>
    <p spaces-before="0">
      <code>samlp:AssertionArtifact</code> [REQUIRED] - the valid CAS Service Ticket obtained as a response parameter at login. See section <a href="#head2.2.4">2.2.4</a>.
    </p>
  </li>
</ul>

<p spaces-before="0">



<a name="head4.2.4"></p>

<h3 spaces-before="0">
  <strong x-id="1">4.2.4 Example of /samlValidate POST request</strong>
</h3>

<pre><code class="bash">POST /cas/samlValidate?TARGET=
Host: cas.example.com
Content-Length: 491
Content-Type: text/xml
</code></pre>

<pre><code class="xml">&lt;SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/"&gt;
    &lt;SOAP-ENV:Header/&gt;
    &lt;SOAP-ENV:Body&gt;
        &lt;samlp:Request xmlns:samlp="urn:oasis:names:tc:SAML:1.0:protocol" MajorVersion="1" MinorVersion="1" RequestID="_192.168.16.51.1024506224022" IssueInstant="2002-06-19T17:03:44.022Z"&gt;
            &lt;samlp:AssertionArtifact&gt;ST-1-u4hrm3td92cLxpCvrjylcas.example.com&lt;/samlp:AssertionArtifact&gt;
        &lt;/samlp:Request&gt;
    &lt;/SOAP-ENV:Body&gt;
&lt;/SOAP-ENV:Envelope&gt;
</code></pre>

<p spaces-before="0">


<a name="head4.2.5"></p>

<h3 spaces-before="0">
  <strong x-id="1">4.2.5 SAML response</strong>
</h3>

<p spaces-before="0">
  CAS Server response to a <code>/samlValidate</code> request. MUST be a SAML 1.1 response.
</p>

<p spaces-before="0">
  Example SAML 1.1 validation response:
</p>

<pre><code class="xml">
&lt;SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/"&gt;
  &lt;SOAP-ENV:Header /&gt;
  &lt;SOAP-ENV:Body&gt;
    &lt;Response xmlns="urn:oasis:names:tc:SAML:1.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:1.0:assertion"
    xmlns:samlp="urn:oasis:names:tc:SAML:1.0:protocol" xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" IssueInstant="2008-12-10T14:12:14.817Z"
    MajorVersion="1" MinorVersion="1" Recipient="https://eiger.iad.vt.edu/dat/home.do"
    ResponseID="_5c94b5431c540365e5a70b2874b75996"&gt;
      &lt;Status&gt;
        &lt;StatusCode Value="samlp:Success"&gt;
        &lt;/StatusCode&gt;
      &lt;/Status&gt;
      &lt;Assertion xmlns="urn:oasis:names:tc:SAML:1.0:assertion" AssertionID="_e5c23ff7a3889e12fa01802a47331653"
      IssueInstant="2008-12-10T14:12:14.817Z" Issuer="localhost" MajorVersion="1"
      MinorVersion="1"&gt;
        &lt;Conditions NotBefore="2008-12-10T14:12:14.817Z" NotOnOrAfter="2008-12-10T14:12:44.817Z"&gt;
          &lt;AudienceRestrictionCondition&gt;
            &lt;Audience&gt;
              https://some-service.example.com/app/
            &lt;/Audience&gt;
          &lt;/AudienceRestrictionCondition&gt;
        &lt;/Conditions&gt;
        &lt;AttributeStatement&gt;
          &lt;Subject&gt;
            &lt;NameIdentifier&gt;johnq&lt;/NameIdentifier&gt;
            &lt;SubjectConfirmation&gt;
              &lt;ConfirmationMethod&gt;
                urn:oasis:names:tc:SAML:1.0:cm:artifact
              &lt;/ConfirmationMethod&gt;
            &lt;/SubjectConfirmation&gt;
          &lt;/Subject&gt;
          &lt;Attribute AttributeName="uid" AttributeNamespace="http://www.ja-sig.org/products/cas/"&gt;
            &lt;AttributeValue&gt;12345&lt;/AttributeValue&gt;
          &lt;/Attribute&gt;
          &lt;Attribute AttributeName="groupMembership" AttributeNamespace="http://www.ja-sig.org/products/cas/"&gt;
            &lt;AttributeValue&gt;
              uugid=middleware.staff,ou=Groups,dc=vt,dc=edu
            &lt;/AttributeValue&gt;
          &lt;/Attribute&gt;
          &lt;Attribute AttributeName="eduPersonAffiliation" AttributeNamespace="http://www.ja-sig.org/products/cas/"&gt;
            &lt;AttributeValue&gt;staff&lt;/AttributeValue&gt;
          &lt;/Attribute&gt;
          &lt;Attribute AttributeName="accountState" AttributeNamespace="http://www.ja-sig.org/products/cas/"&gt;
            &lt;AttributeValue&gt;ACTIVE&lt;/AttributeValue&gt;
          &lt;/Attribute&gt;
        &lt;/AttributeStatement&gt;
        &lt;AuthenticationStatement AuthenticationInstant="2008-12-10T14:12:14.741Z"
        AuthenticationMethod="urn:oasis:names:tc:SAML:1.0:am:password"&gt;
          &lt;Subject&gt;
            &lt;NameIdentifier&gt;johnq&lt;/NameIdentifier&gt;
            &lt;SubjectConfirmation&gt;
              &lt;ConfirmationMethod&gt;
                urn:oasis:names:tc:SAML:1.0:cm:artifact
              &lt;/ConfirmationMethod&gt;
            &lt;/SubjectConfirmation&gt;
          &lt;/Subject&gt;
        &lt;/AuthenticationStatement&gt;
      &lt;/Assertion&gt;
    &lt;/Response&gt;
  &lt;/SOAP-ENV:Body&gt;
&lt;/SOAP-ENV:Envelope&gt;
</code></pre>

<p spaces-before="0">


<a name="head4.2.5.1"></p>

<h3 spaces-before="0">
  <strong x-id="1">4.2.5.1 SAML CAS response attributes</strong>
</h3>

<p spaces-before="0">
  The following additional attributes might be provided within the SAML response:
</p>

<ul>
  <li>
    <code>longTermAuthenticationRequestTokenUsed</code> - If Long Term Ticket Granting Tickets (Remember-Me) are supported by the CAS Server (see Section <a href="#head4.1">4.1</a>), the SAML response MUST include this attribute to indicate remembered logins to the CAS client.
  </li>
</ul>

<p spaces-before="0">


<a name="head_appdx_a"></p>
<h1 spaces-before="0">
  <strong x-id="1">Appendix A: CAS response XML schema</strong>
</h1>

<pre><code class="xml">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           xmlns:cas="http://www.yale.edu/tp/cas"
           targetNamespace="http://www.yale.edu/tp/cas"
           elementFormDefault="qualified"&gt;
  &lt;xs:annotation&gt;
    &lt;xs:documentation&gt;
      The following is the schema for the Central Authentication Service (CAS) version 3.0 protocol response.&lt;br /&gt;
      This covers the responses for the following endpoints: /serviceValidate, /proxyValidate, /p3/serviceValidate, /p3/proxyValidate, /proxy.&lt;br /&gt;
      This specification is subject to change.&lt;br /&gt;

      Schema version: 3.0.3&lt;br /&gt;

      History:&lt;br /&gt;
      3.0   initial version for CAS 3.0 protocol spec &lt;br /&gt;
      3.0.3 fixed attributes memberOf / xs:any clash, added documentation.&lt;br /&gt;
    &lt;/xs:documentation&gt;
  &lt;/xs:annotation&gt;
  &lt;xs:element name="serviceResponse" type="cas:ServiceResponseType"&gt;
    &lt;xs:annotation&gt;
      &lt;xs:documentation&gt;The service Response.&lt;/xs:documentation&gt;
    &lt;/xs:annotation&gt;
  &lt;/xs:element&gt;
  &lt;xs:complexType name="ServiceResponseType"&gt;
    &lt;xs:choice&gt;
      &lt;xs:element name="authenticationSuccess" type="cas:AuthenticationSuccessType"/&gt;
      &lt;xs:element name="authenticationFailure" type="cas:AuthenticationFailureType"/&gt;
      &lt;xs:element name="proxySuccess" type="cas:ProxySuccessType"/&gt;
      &lt;xs:element name="proxyFailure" type="cas:ProxyFailureType"/&gt;
    &lt;/xs:choice&gt;
  &lt;/xs:complexType&gt;
  &lt;xs:complexType name="AuthenticationSuccessType"&gt;
    &lt;xs:sequence&gt;
      &lt;xs:element name="user" type="xs:string"&gt;
        &lt;xs:annotation&gt;
          &lt;xs:documentation&gt;The username which authenticated successfully.&lt;/xs:documentation&gt;
        &lt;/xs:annotation&gt;
      &lt;/xs:element&gt;
      &lt;xs:element name="attributes" type="cas:AttributesType" minOccurs="0"&gt;
        &lt;xs:annotation&gt;
          &lt;xs:documentation&gt;Optional attributes.&lt;/xs:documentation&gt;
        &lt;/xs:annotation&gt;
      &lt;/xs:element&gt;
      &lt;xs:element name="proxyGrantingTicket" type="xs:string" minOccurs="0"&gt;
        &lt;xs:annotation&gt;
          &lt;xs:documentation&gt;Optional PGT.&lt;/xs:documentation&gt;
        &lt;/xs:annotation&gt;
      &lt;/xs:element&gt;
      &lt;xs:element name="proxies" type="cas:ProxiesType" minOccurs="0"&gt;
        &lt;xs:annotation&gt;
          &lt;xs:documentation&gt;Optional type of proxies.&lt;/xs:documentation&gt;
        &lt;/xs:annotation&gt;
      &lt;/xs:element&gt;
    &lt;/xs:sequence&gt;
  &lt;/xs:complexType&gt;
  &lt;xs:complexType name="ProxiesType"&gt;
    &lt;xs:sequence&gt;
      &lt;xs:element name="proxy" type="xs:string" maxOccurs="unbounded"/&gt;
    &lt;/xs:sequence&gt;
  &lt;/xs:complexType&gt;
  &lt;xs:complexType name="AuthenticationFailureType"&gt;
    &lt;xs:simpleContent&gt;
      &lt;xs:extension base="xs:string"&gt;
        &lt;xs:attribute name="code" type="xs:string" use="required"&gt;
          &lt;xs:annotation&gt;
            &lt;xs:documentation&gt;The error code on authentication failure.&lt;/xs:documentation&gt;
          &lt;/xs:annotation&gt;
        &lt;/xs:attribute&gt;
      &lt;/xs:extension&gt;
    &lt;/xs:simpleContent&gt;
  &lt;/xs:complexType&gt;
  &lt;xs:complexType name="ProxySuccessType"&gt;
    &lt;xs:sequence&gt;
      &lt;xs:element name="proxyTicket" type="xs:string"&gt;
        &lt;xs:annotation&gt;
          &lt;xs:documentation&gt;The PT.&lt;/xs:documentation&gt;
        &lt;/xs:annotation&gt;
      &lt;/xs:element&gt;
    &lt;/xs:sequence&gt;
  &lt;/xs:complexType&gt;
  &lt;xs:complexType name="ProxyFailureType"&gt;
    &lt;xs:simpleContent&gt;
      &lt;xs:extension base="xs:string"&gt;
        &lt;xs:attribute name="code" type="xs:string" use="required"&gt;
          &lt;xs:annotation&gt;
            &lt;xs:documentation&gt;The error code on proxy failure.&lt;/xs:documentation&gt;
          &lt;/xs:annotation&gt;
        &lt;/xs:attribute&gt;
      &lt;/xs:extension&gt;
    &lt;/xs:simpleContent&gt;
  &lt;/xs:complexType&gt;
  &lt;xs:complexType name="AttributesType"&gt;
    &lt;xs:sequence&gt;
      &lt;xs:element name="authenticationDate" type="xs:dateTime" minOccurs="1" maxOccurs="1"/&gt;
      &lt;xs:element name="longTermAuthenticationRequestTokenUsed" type="xs:boolean" minOccurs="1" maxOccurs="1"&gt;
        &lt;xs:annotation&gt;
          &lt;xs:documentation&gt;true if a long-term (Remember-Me) token was used&lt;/xs:documentation&gt;
        &lt;/xs:annotation&gt;
      &lt;/xs:element&gt;
      &lt;xs:element name="isFromNewLogin" type="xs:boolean" minOccurs="1" maxOccurs="1"&gt;
        &lt;xs:annotation&gt;
          &lt;xs:documentation&gt;true if this was from a new, interactive login. If login was from a non-interactive login (e.g. Remember-Me), this value is false or might be omitted.&lt;/xs:documentation&gt;
        &lt;/xs:annotation&gt;
      &lt;/xs:element&gt;
      &lt;xs:any minOccurs="0" maxOccurs="unbounded" processContents="lax"&gt;
        &lt;xs:annotation&gt;
          &lt;xs:documentation&gt;Any user specific attribute elements. May contain memberOf or any other elements.&lt;/xs:documentation&gt;
        &lt;/xs:annotation&gt;
      &lt;/xs:any&gt;
    &lt;/xs:sequence&gt;
  &lt;/xs:complexType&gt;
&lt;/xs:schema&gt;
</code></pre>

<blockquote spaces-before="0">
  <p spaces-before="0">
    Note: As user attributes can be extended by the CAS Server implementer (see <xs:any> schema definition), it is RECOMMENDED to form custom attributes as using the following format:
  </p>
</blockquote>

<pre><code class="xml">&lt;cas:attributes&gt;
    ...
    &lt;cas:[attribute-name]&gt;VALUE&lt;/cas:[attribute-name]&gt;
&lt;/cas:attributes&gt;
</code></pre>

<blockquote spaces-before="0">
  <p spaces-before="0">
    Example response with custom attribute:
  </p>
</blockquote>

<pre><code class="xml">&lt;cas:attributes&gt;
    &lt;cas:authenticationDate&gt;2015-11-12T09:30:10Z&lt;/cas:authenticationDate&gt;
    &lt;cas:longTermAuthenticationRequestTokenUsed&gt;true&lt;/cas:longTermAuthenticationRequestTokenUsed&gt;
    &lt;cas:isFromNewLogin&gt;true&lt;/cas:isFromNewLogin&gt;
    &lt;cas:myAttribute&gt;myValue&lt;/cas:myAttribute&gt;
&lt;/cas:attributes&gt;
</code></pre>

<p spaces-before="0">

<a name="head_appdx_b"></p>
<h1 spaces-before="0">
  <strong x-id="1">Appendix B: Safe redirection</strong>
</h1>

<p spaces-before="0">
  After a successful login, safely redirecting the client from CAS to its final destination must be handled with care. In most cases, the client has sent credentials to the CAS server over a POST request. By this specification, the CAS server must then forward the user to the application with a GET request.
</p>

<p spaces-before="0">
  The HTTP/1.1 RFC[<a href="#3">3</a>] provides a response code of 303: See Other, which provides for the desired behavior: a script that receives data through a POST request can, through a 303 redirection, forward the browser to another URL through a GET request. However, not all browsers have implemented this behavior correctly.
</p>

<p spaces-before="0">
  The RECOMMENDED method of redirection is thus JavaScript. A page containing a <code>window.location.href</code> in the following manner performs adequately:
</p>

<pre><code class="html"> &lt;html&gt;
    &lt;head&gt;
        &lt;title&gt;Yale Central Authentication Service&lt;/title&gt;
        &lt;script&gt;
            window.location.href="https://portal.yale.edu/Login?ticket=ST-..."
mce_href="https://portal.yale.edu/Login?ticket=ST-...";`
       &lt;/script&gt;
    &lt;/head&gt;
    &lt;body&gt;
        &lt;noscript&gt;
            &lt;p&gt;CAS login successful.&lt;/p&gt;
            &lt;p&gt;  Click &lt;a xhref="https://portal.yale.edu/Login?ticket=ST-..."
mce_href="https://portal.yale.edu/Login?ticket=ST-..."&gt;here&lt;/a&gt;
            to access the service you requested.&lt;br /&gt;  &lt;/p&gt;
        &lt;/noscript&gt;
    &lt;/body&gt;
 &lt;/html&gt;
</code></pre>

<p spaces-before="0">
  Additionally, CAS should disable browser caching by setting all of the various cache-related headers:
</p>

<ul>
  <li>
    <p spaces-before="0">
      Pragma: no-cache
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Cache-Control: no-store
    </p>
  </li>
  <li>
    <p spaces-before="0">
      Expires: [RFC 1123[<a href="#6">6</a>] date equal to or before now]
    </p>
  </li>
</ul>

<p spaces-before="0">
  The introduction of the login ticket removed the possibility of CAS accepting credentials that were cached and replayed by a browser. However, early versions of Apple's Safari browser contained a bug where through usage of the Back button, Safari could be coerced into presenting the client's credentials to the service it is trying to access. CAS can prevent this behavior by not automatically redirecting if it detects that the remote browser is one of these early versions of Safari. Instead, CAS should display a page that states login was successful, and provide a link to the requested service. The client must then manually click to proceed.
</p>

<p spaces-before="0">



<a name="head_appdx_c"></p>
<h1 spaces-before="0">
  <strong x-id="1">Appendix C: Logout XML document</strong>
</h1>

<p spaces-before="0">
  When SLO is supported by the CAS Server, it will callback to each of the services that are registered with the system and send a POST request with the following SAML Logout Request XML document:
</p>

<pre><code class="xml">  &lt;samlp:LogoutRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol"
     ID="[RANDOM ID]" Version="2.0" IssueInstant="[CURRENT DATE/TIME]"&gt;
    &lt;saml:NameID xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion"&gt;
      @NOT_USED@
    &lt;/saml:NameID&gt;
    &lt;samlp:SessionIndex&gt;[SESSION IDENTIFIER]&lt;/samlp:SessionIndex&gt;
  &lt;/samlp:LogoutRequest&gt;`
</code></pre>

<p spaces-before="0">


<a name="head_appdx_d"></p>
<h1 spaces-before="0">
  <strong x-id="1">Appendix D: References</strong>
</h1>

<p spaces-before="0">
  <a name="1">[1] Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels", <a href="http://www.ietf.org/rfc/rfc2119.txt">RFC 2119</a>, Harvard University, March 1997.</p> 
  
  <p spaces-before="0">
    <a name="2">[2] Berners-Lee, T., Fielding, R., Frystyk, H., "Hypertext Transfer Protocol - HTTP/1.0", <a href="http://www.ietf.org/rfc/rfc1945.txt">RFC 1945</a>, MIT/LCS, UC Irvine, MIT/LCS, May 1996.</p> 
    
    <p spaces-before="0">
      <a name="3">[3] Fielding, R., Gettys, J., Mogul, J., Frystyk, H., Masinter, L., Leach, P., Berners-Lee, T., "Hypertext Transfer Protocol - HTTP/1.1", <a href="http://www.ietf.org/rfc/rfc2068.txt">RFC 2068</a>, UC Irvine, Compaq/W3C, Compaq, W3C/MIT, Xerox, Microsoft, W3C/MIT, June 1999.</p> 
      
      <p spaces-before="0">
        <a name="4">[4] Berners-Lee, T., Masinter, L., and MaCahill, M., "Uniform Resource Locators (URL)", <a href="http://www.ietf.org/rfc/rfc1738.txt">RFC 1738</a>, CERN, Xerox Corporation, University of Minnesota, December 1994.</p> 
        
        <p spaces-before="0">
          <a name="5">[5] Kristol, D., Montulli, L., "HTTP State Management Mechanism", <a href="http://www.ietf.org/rfc/rfc2965.txt">RFC 2965</a>, Bell Laboratories/Lucent Technologies, Epinions.com, Inc., October 2000.</p> 
          
          <p spaces-before="0">
            <a name="6">[6] Braden, R., "Requirements for Internet Hosts - Application and Support", <a href="http://www.ietf.org/rfc/rfc1123.txt">RFC 1123</a>, Internet Engineering Task Force, October 1989.</p> 
            
            <p spaces-before="0">
              <a name="7">[7] OASIS SAML 1.1 standard, saml.xml.org, December 2009.</p> 
              
              <p spaces-before="0">
                <a name="8">[8] <a href="http://www.Apereo.org/cas">Apereo CAS Server</a> reference implementation</p> 
                
                <p spaces-before="0">


<a name="head_appdx_e"></p>
<h1 spaces-before="0">
  <strong x-id="1">Appendix E: CAS License</strong>
</h1>

<p spaces-before="0">
  Licensed to Apereo under one or more contributor license agreements. See the NOTICE file distributed with this work for additional information regarding copyright ownership. Apereo licenses this file to you under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License.  You may obtain a copy of the License at the following location:
</p>

<p spaces-before="0">
  http://www.apache.org/licenses/LICENSE-2.0
</p>

<p spaces-before="0">
  Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.  See the License for the specific language governing permissions and limitations under the License.
</p>

<p spaces-before="0">


<a name="head_appdx_f"></p>
<h1 spaces-before="0">
  <strong x-id="1">Appendix F: YALE License</strong>
</h1>

<p spaces-before="0">
  Copyright (c) 2000-2005 Yale University.
</p>

<p spaces-before="0">
  THIS SOFTWARE IS PROVIDED "AS IS," AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE, ARE EXPRESSLY DISCLAIMED. IN NO EVENT SHALL YALE UNIVERSITY OR ITS EMPLOYEES BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED, THE COSTS OF PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED IN ADVANCE OF THE POSSIBILITY OF SUCH DAMAGE.
</p>

<p spaces-before="0">
  Redistribution and use of this software in source or binary forms, with or without modification, are permitted, provided that the following conditions are met:
</p>

<ol start="1">
  <li>
    <p spaces-before="0">
      Any redistribution must include the above copyright notice and disclaimer and this list of conditions in any related documentation and, if feasible, in the redistributed software.
    </p>
  </li>
  
  <li>
    <p spaces-before="0">
      Any redistribution must include the acknowledgment, "This product includes software developed by Yale University," in any related documentation and, if feasible, in the redistributed software.
    </p>
  </li>
  
  <li>
    <p spaces-before="0">
      The names "Yale" and "Yale University" must not be used to endorse or promote products derived from this software.
    </p>
  </li>
</ol>

<p spaces-before="0">



<a name="head_appdx_g"></p>
<h1 spaces-before="0">
  <strong x-id="1">Appendix G: Changes to this Document</strong>
</h1>

<p spaces-before="0">
  May 4, 2005: v1.0 - initial release for CAS 1.0 and CAS 2.0, Copyright © 2005, Yale University
</p>

<p spaces-before="0">
  March 2, 2012: v1.0.1 - fixed "noscropt" typo. apetro per amazurek with credit to Faraz Khan at ASU for catching the typo.
</p>

<p spaces-before="0">
  April, 2013: v3.0 - CAS 3.0 protocol, Apereo copyright, Apache License 2.0
</p>

<p spaces-before="0">
  January, 2014: v3.0.1 - Attribute occurrence
</p>

<p spaces-before="0">
  September, 2015: v3.0.2 - Format parameter
</p>

<p spaces-before="0">
  December, 2017: v3.0.3 - Fixed ServiceValidate XSD schema
</p>
