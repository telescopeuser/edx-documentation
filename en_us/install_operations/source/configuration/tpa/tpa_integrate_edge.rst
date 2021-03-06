.. _Enabling Third Party Authentication Edge:

####################################################
Integrating Third Party Authentication with edX Edge
####################################################

Institutions that have partner memberships with edX can integrate third party
authentication between their campus or institutional authentication systems and
the edX Edge site. Learners at sites that enable third party authentication can
use their campus credentials to authenticate into edX Edge.

These procedures require collaboration between members of the development
operations (DevOps) and information technology (IT) teams at your partner
institution and the DevOps team at edX, facilitated by your edX partner
manager.

.. contents::
   :local:
   :depth: 2

***********************************************
Integrating with Shibboleth (SAML) Systems
***********************************************

Security Assertion Markup Language, version 2.0 (SAML 2.0) is the standard that
edX uses for the exchange of authentication and authorization data with
institutional partners. Because it is built with SAML, this service
is compatible with the Shibboleth single sign on system.

In the exchange of authentication and authorization data, your edX partner
institution is an identity provider (IdP) that securely asserts the identity
and access rights of a set of users. EdX is the service provider (SP) that
allows the users access on the basis of those credentials.

All procedures described in this section should be performed by a member of
your institution's IT team who is familiar with your institutional Shibboleth
system. The designated IT contact must have access to test and production
accounts, and be able to make configuration changes.

=======================================
Validate Integration
=======================================

Integrating an institutional IdP with edX Edge requires a preliminary
validation phase.

#. All procedures are completed between a test Shibboleth account at the
   partner institution and the edX staging instance ("edX stage").

#. After validation, all procedures are completed between the partner's
   production system and edX Edge.

Your designated IT contact must set up or identify a Shibboleth test account,
and securely transmit its credentials to edX, before integration testing can
begin.

.. note:: The edX DevOps team prefers PGP encryption of account credentials.
 Other secure methods for transmitting credentials can be accommodated if
 necessary.

=======================================
Obtain edX SAML Metadata
=======================================

During the testing phase, you use a `test metadata file`_ to obtain the
information necessary for interaction between edX stage and your test account.

When testing is complete, you access the service provider SAML metadata for
Edge in an `XML file`_ on the Edge website. This file contains the information
necessary for interaction between Edge as service provider and your campus
production Shibboleth system as identity provider.

============================================
Add edX as a Service Provider
============================================

On your Shibboleth system, use the SAML metadata obtained from the XML file to
add edX stage, and then edX Edge, to your whitelist of authorized service
providers.

For example, you might add the following information to your
``$IDP_HOME/conf/relying-party.xml`` file for edX Edge.

.. code:: xml

  <MetadataProvider xsi:type="FileBackedHTTPMetadataProvider" xmlns="urn:mace:shibboleth:2.0:metadata"
                    id="edxEdgeMetadata"
                    metadataURL="https://edge.edx.org/auth/saml/metadata.xml"
                    backingFile="/tmp/idp-metadata-edx-edge.xml" />

For more information about defining a metadata source on a Shibboleth system,
see the `Shibboleth configuration wiki`_.

.. _Configure User Attributes:

=============================
Configure User Attributes
=============================

You work with edX to ensure that your identity provider will assert the user
information that you want learners to see during initial sign in on edX Edge.
When you :ref:`send your Shibboleth configuration data<Send Shibboleth
Configuration Data to edX>` to your institution's edX partner manager, you
include any customizations to the attributes that will be asserted.

Selecting User Attributes to Assert
************************************

By default, the edX platform uses these attributes if your system provides
them.

* User identifier: ``userid``, ``urn:oid:0.9.2342.19200300.100.1.1``.
  Required. This value is used to associate the user's edX account with the
  campus account. It is not displayed to users.
* Full name: ``commonName``, ``urn:oid:2.5.4.3``
* First name: ``givenName``, ``urn:oid:2.5.4.42``
* Last name: ``surname``, ``urn:oid:2.5.4.4``
* Suggested username: ``userid``, ``urn:oid:0.9.2342.19200300.100.1.1``
* Email address: ``mail``, ``urn:oid:0.9.2342.19200300.100.1.3``

At your request, edX can configure Edge to use only some of these attributes.
The only required attribute is the user identifier. If you choose not to
provide an attribute, learners are prompted to enter that information
themselves during initial sign in.

Specifying Alternative Attributes
***********************************

You can identify different attributes to assert user information than those
listed above. For example, you might want to send the suggested username as
``eduPersonPrincipalName`` instead of ``userid``.

Restricting Access with Attribute Assertions
********************************************

You can restrict access to edX Edge to a subset of your users using attributes
defined on your Shibboleth system. For example, you might want to allow
currently matriculated students to sign in to Edge, but not alumni. The
``eduPersonEntitlement`` attribute can be used to restrict access in this way.

.. note:: If you want access to be restricted to certain users, be sure to
 let edX know what provider assertions to use to determine access rights.

.. _Send Shibboleth Configuration Data to edX:

======================================================
Send Shibboleth Configuration Data to edX
======================================================

To complete the integration between a Shibboleth system and an edX system, you
send a copy of the assertion document with the following information to your
institution's edX partner manager.

* Metadata: The URL for your Shibboleth IdP metadata XML file.

* User Attributes: A list of the values that you want your Shibboleth system to
  assert when users sign in to edX Edge.

  For more information about how you can work with edX to configure user
  attributes effectively, see :ref:`Configure User Attributes`.

You send this information for both your test and production accounts so that
integration can be validated.

Your edX partner manager notifies you when integration with your IdP is
complete.

========================
Test Edge Registration
========================

To verify that users can use their campus or institutional credentials for your
IdP to sign in to Edge, follow these steps.

#. Go to the `Edge registration`_ page. The page should include the
   institutional sign in button.

   .. image:: ../../Images/tpa_signin.png
     :alt: Screen shot of an LMS sign in page with a button labeled "Use my
         institutional/campus credentials" at the bottom.

#. Select **Use my institutional/campus credentials**. The list of providers
   that appears should include your IdP.

   .. image:: ../../Images/tpa_inst_list.png
     :alt: Screen shot of the list of enabled IdPs. Each IdP name is linked to
         the sign in page for the corresponding authentication system.

#. Select your own IdP. The landing page for your Shibboleth system should
   open.

.. future: other SAML2-compliant identity providers
.. Alison 15 Jul 2015


.. include:: ../../../../links/links.rst
