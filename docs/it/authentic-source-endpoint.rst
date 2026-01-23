.. include:: ../common/common_definitions.rst


.. role:: raw-html(raw)
  :format: html

Endpoint delle Fonti Autentiche
-------------------------------

Catalogo degli e-Service PDND delle Fonti Autentiche
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Le Fonti Autentiche pubbliche DEVONO realizzare e rendere disponibile tramite PDND il seguente e-service al fine di rilasciare al Fornitore di Attestati Elettronici gli Attributi dell'Utente necessari per l'emissione di un Attestato Elettronico.

.. only:: html

  .. note::
    La Specifica OpenAPI completa è disponibile :raw-html:`<a href="OAS3-PDND-AS.html" target="_blank">qui</a>`.

.. only:: latex

  .. note::
    La Specifica OpenAPI completa è disponibile :ref:`e-service-pdnd-template:Specifica OpenAPI della Fonte Autentica PDND`.

Get Attribute Claims
"""""""""""""""""""""""""""""""""""

.. _authentic-source-endpoint-get-attribute-claims:
.. list-table::
  :class: longtable
  :widths: 20 80
  :stub-columns: 1

  * - **Descrizione**
    - Questo servizio fornisce al Fornitore di Attestati Elettronici tutti gli attributi dell'Utente necessari per il rilascio di un Attestato Elettronico.
  * - **Erogatore**
    - Fonte Autentica
  * - **Fruitore**
    - Fornitore di Attestato Elettronico

.. note::
  La Fonte Autentica e il Credential Issuer DEVONO implementare la logica necessaria per tenere traccia delle richieste e delle risposte scambiate tramite questo e-Service, al fine di essere in grado di correlarle con la relativa emissione di un Attestato Elettronico. In particolare,
    - entrambi DEVONO salvare il valore ``jti`` contenuto nel payload del token Agid-JWT-Signature della richiesta per gestire i Segnali relativi alla disponibilità degli Attributi utili all'emissione in *deferred* di un Attestato Elettronico (vedere :ref:`signal-hub-endpoint:Elaborazione dei Segnali`);
    - la Fonte Autentica DEVE registrare il valore datetime fornito all'interno del parametro ``last_updated``, che indica data e orario dell'ultima volta che gli Attributi dell'Utente sono stati aggiornati nel database della Fonte Autentica;
    - il Credential Issuer DEVE leggere il valore ``last_updated`` ricevuto nella risposta per essere in grado di verificare se gli Attributi dell'Utente sono cambiati dall'ultima emissione di un Attestato Elettronico.

La risposta in caso di successo (HTTP 200) restituisce un oggetto ``CredentialClaimsResponse`` formattato come **Signed JWT**.

Essendo il token di risposta firmato, il Credential Issuer (Fruitore) DEVE verificare la firma per garantire l'integrità e l'autenticità dei dati ricevuti dalla Fonte Autentica.
Il pattern tecnico per la verifica della firma DEVE seguire il modello di interoperabilità PDND:

1. La Fonte Autentica **DEVE** firmare il JWT di risposta utilizzando una chiave privata la cui corrispondente chiave pubblica è stata caricata nel proprio **"Portachiavi Erogatore"** sulla piattaforma PDND.
2. Il Credential Issuer **DEVE** recuperare la chiave pubblica necessaria alla verifica tramite le API dell'infrastruttura PDND, utilizzando il valore del parametro ``kid`` presente nell'header JOSE del JWT di risposta.

.. warning::
  Non sono previsti meccanismi alternativi di distribuzione del materiale crittografico (es. endpoint ``.well-known`` pubblici o distribuzione *out-of-band*). La gestione del trust DEVE rimanere all'interno del perimetro della piattaforma PDND.
