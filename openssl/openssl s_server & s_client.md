

示例：

```
更新动态链接库名字列表
sudo ldconfig

openssl s_server -accept 443 -key server.pem -cert server.pem -debug -msg

openssl s_client -connect 127.0.0.1:443 -debug -msg


ln -s /usr/local/ssl3/lib/libssl.so.3 /usr/lib64/libssl.so.3
ln -s /usr/local/ssl3/lib/libcrypto.so.3 /usr/lib64/libcrypto.so.3

sudo ln -s /usr/local/ssl3/lib64/libssl.so.1.1 /usr/local/lib64/libssl.so.1.1
sudo ln -s /usr/local/ssl3/lib64/libcrypto.so.1.1 /usr/local/lib64/libcrypto.so.1.1
```




# openssl s_server 命令

```
[root@centos ~]#openssl s_server --help
Usage: s_server [options]
Valid options are:
 -help                      Display this summary
 -port +int                 TCP/IP port to listen on for connections (default is 4433)
 -accept val                TCP/IP optional host and port to listen on for connections (default is *:4433)
 -unix val                  Unix domain socket to accept on
 -4                         Use IPv4 only
 -6                         Use IPv6 only
 -unlink                    For -unix, unlink existing socket first
 -context val               Set session ID context
 -verify int                Turn on peer certificate verification
 -Verify int                Turn on peer certificate verification, must have a cert
 -cert infile               Certificate file to use; default is server.pem
 -nameopt val               Various certificate name options
 -naccept +int              Terminate after #num connections
 -serverinfo val            PEM serverinfo file for certificate
 -certform PEM|DER          Certificate format (PEM or DER) PEM default
 -key val                   Private Key if not in -cert; default is server.pem
 -keyform format            Key format (PEM, DER or ENGINE) PEM default
 -pass val                  Private key file pass phrase source
 -dcert infile              Second certificate file to use (usually for DSA)
 -dhparam infile            DH parameters file to use
 -dcertform PEM|DER         Second certificate format (PEM or DER) PEM default
 -dkey infile               Second private key file to use (usually for DSA)
 -dkeyform PEM|DER          Second key format (PEM, DER or ENGINE) PEM default
 -dpass val                 Second private key file pass phrase source
 -nbio_test                 Test with the non-blocking test bio
 -crlf                      Convert LF from terminal into CRLF
 -debug                     Print more output
 -msg                       Show protocol messages
 -msgfile outfile           File to send output of -msg or -trace, instead of stdout
 -state                     Print the SSL states
 -CAfile infile             PEM format file of CA's
 -CApath dir                PEM format directory of CA's
 -no-CAfile                 Do not load the default certificates file
 -no-CApath                 Do not load certificates from the default certificates directory
 -nocert                    Don't use any certificates (Anon-DH)
 -quiet                     No server output
 -no_resume_ephemeral       Disable caching and tickets if ephemeral (EC)DH is used
 -www                       Respond to a 'GET /' with a status page
 -WWW                       Respond to a 'GET with the file ./path
 -servername val            Servername for HostName TLS extension
 -servername_fatal          mismatch send fatal alert (default warning alert)
 -cert2 infile              Certificate file to use for servername; default isserver2.pem
 -key2 infile               -Private Key file to use for servername if not in -cert2
 -tlsextdebug               Hex dump of all TLS extensions received
 -HTTP                      Like -WWW but ./path includes HTTP headers
 -id_prefix val             Generate SSL/TLS session IDs prefixed by arg
 -rand val                  Load the file(s) into the random number generator
 -writerand outfile         Write random data to the specified file
 -keymatexport val          Export keying material using label
 -keymatexportlen +int      Export len bytes of keying material (default 20)
 -CRL infile                CRL file to use
 -crl_download              Download CRL from distribution points
 -cert_chain infile         certificate chain file in PEM format
 -dcert_chain infile        second certificate chain file in PEM format
 -chainCApath dir           use dir as certificate store path to build CA certificate chain
 -verifyCApath dir          use dir as certificate store path to verify CA certificate
 -no_cache                  Disable session cache
 -ext_cache                 Disable internal cache, setup and use external cache
 -CRLform PEM|DER           CRL format (PEM or DER) PEM is default
 -verify_return_error       Close connection on verification error
 -verify_quiet              No verify output except verify errors
 -build_chain               Build certificate chain
 -chainCAfile infile        CA file for certificate chain (PEM format)
 -verifyCAfile infile       CA file for certificate verification (PEM format)
 -ign_eof                   ignore input eof (default when -quiet)
 -no_ign_eof                Do not ignore input eof
 -status                    Request certificate status from server
 -status_verbose            Print more output in certificate status callback
 -status_timeout int        Status request responder timeout
 -status_url val            Status request fallback URL
 -status_file infile        File containing DER encoded OCSP Response
 -security_debug            Print output from SSL/TLS security framework
 -security_debug_verbose    Print more output from SSL/TLS security framework
 -brief                     Restrict output to brief summary of connection parameters
 -rev                       act as a simple test server which just sends back with the received text reversed
 -async                     Operate in asynchronous mode
 -ssl_config val            Configure SSL_CTX using the configuration 'val'
 -max_send_frag +int        Maximum Size of send frames
 -split_send_frag +int      Size used to split data for encrypt pipelines
 -max_pipelines +int        Maximum number of encrypt/decrypt pipelines to be used
 -read_buf +int             Default read buffer size to be used for connections
 -no_ssl3                   Just disable SSLv3
 -no_tls1                   Just disable TLSv1
 -no_tls1_1                 Just disable TLSv1.1
 -no_tls1_2                 Just disable TLSv1.2
 -no_tls1_3                 Just disable TLSv1.3
 -bugs                      Turn on SSL bug compatibility
 -no_comp                   Disable SSL/TLS compression (default)
 -comp                      Use SSL/TLS-level compression
 -no_ticket                 Disable use of TLS session tickets
 -serverpref                Use server's cipher preferences
 -legacy_renegotiation      Enable use of legacy renegotiation (dangerous)
 -no_renegotiation          Disable all renegotiation.
 -legacy_server_connect     Allow initial connection to servers that don't support RI
 -no_resumption_on_reneg    Disallow session resumption on renegotiation
 -no_legacy_server_connect  Disallow initial connection to servers that don't support RI
 -allow_no_dhe_kex          In TLSv1.3 allow non-(ec)dhe based key exchange on resumption
 -prioritize_chacha         Prioritize ChaCha ciphers when preferred by clients
 -strict                    Enforce strict certificate checks as per TLS standard
 -sigalgs val               Signature algorithms to support (colon-separated list)
 -client_sigalgs val        Signature algorithms to support for client certificate authentication (colon-separated list)
 -groups val                Groups to advertise (colon-separated list)
 -curves val                Groups to advertise (colon-separated list)
 -named_curve val           Elliptic curve used for ECDHE (server-side only)
 -cipher val                Specify TLSv1.2 and below cipher list to be used
 -ciphersuites val          Specify TLSv1.3 ciphersuites to be used
 -min_protocol val          Specify the minimum protocol version to be used
 -max_protocol val          Specify the maximum protocol version to be used
 -record_padding val        Block size to pad TLS 1.3 records to.
 -debug_broken_protocol     Perform all sorts of protocol violations for testing purposes
 -no_middlebox              Disable TLSv1.3 middlebox compat mode
 -policy val                adds policy to the acceptable policy set
 -purpose val               certificate chain purpose
 -verify_name val           verification policy name
 -verify_depth int          chain depth limit
 -auth_level int            chain authentication security level
 -attime intmax             verification epoch time
 -verify_hostname val       expected peer hostname
 -verify_email val          expected peer email
 -verify_ip val             expected peer IP address
 -ignore_critical           permit unhandled critical extensions
 -issuer_checks             (deprecated)
 -crl_check                 check leaf certificate revocation
 -crl_check_all             check full chain revocation
 -policy_check              perform rfc5280 policy checks
 -explicit_policy           set policy variable require-explicit-policy
 -inhibit_any               set policy variable inhibit-any-policy
 -inhibit_map               set policy variable inhibit-policy-mapping
 -x509_strict               disable certificate compatibility work-arounds
 -extended_crl              enable extended CRL features
 -use_deltas                use delta CRLs
 -policy_print              print policy processing diagnostics
 -check_ss_sig              check root CA self-signatures
 -trusted_first             search trust store first (default)
 -suiteB_128_only           Suite B 128-bit-only mode
 -suiteB_128                Suite B 128-bit mode allowing 192-bit algorithms
 -suiteB_192                Suite B 192-bit-only mode
 -partial_chain             accept chains anchored by intermediate trust-store CAs
 -no_alt_chains             (deprecated)
 -no_check_time             ignore certificate validity time
 -allow_proxy_certs         allow the use of proxy certificates
 -xkey infile               key for Extended certificates
 -xcert infile              cert for Extended certificates
 -xchain infile             chain for Extended certificates
 -xchain_build              build certificate chain for the extended certificates
 -xcertform PEM|DER         format of Extended certificate (PEM or DER) PEM default
 -xkeyform PEM|DER          format of Extended certificate's key (PEM or DER) PEM default
 -nbio                      Use non-blocking IO
 -psk_identity val          PSK identity to expect
 -psk_hint val              PSK identity hint to use
 -psk val                   PSK in hex (without 0x)
 -psk_session infile        File to read PSK SSL session from
 -srpvfile infile           The verifier file for SRP
 -srpuserseed val           A seed string for a default user salt
 -tls1                      Just talk TLSv1
 -tls1_1                    Just talk TLSv1.1
 -tls1_2                    just talk TLSv1.2
 -tls1_3                    just talk TLSv1.3
 -dtls                      Use any DTLS version
 -timeout                   Enable timeouts
 -mtu +int                  Set link layer MTU
 -listen                    Listen for a DTLS ClientHello with a cookie and then connect
 -stateless                 Require TLSv1.3 cookies
 -dtls1                     Just talk DTLSv1
 -dtls1_2                   Just talk DTLSv1.2
 -no_dhe                    Disable ephemeral DH
 -nextprotoneg val          Set the advertised protocols for the NPN extension (comma-separated list)
 -use_srtp val              Offer SRTP key management with a colon-separated profile list
 -alpn val                  Set the advertised protocols for the ALPN extension (comma-separated list)
 -engine val                Use engine, possibly a hardware device
 -keylogfile outfile        Write TLS secrets to file
 -max_early_data int        The maximum number of bytes of early data as advertised in tickets
 -recv_max_early_data int   The maximum number of bytes of early data (hard limit)
 -early_data                Attempt to read early data
 -num_tickets int           The number of TLSv1.3 session tickets that a server will automatically  issue
 -anti_replay               Switch on anti-replay protection (default)
 -no_anti_replay            Switch off anti-replay protection
```

# openssl s_client 命令

```
[root@centos ~]# openssl s_client --help
Usage: s_client [options]
Valid options are:
 -help                      Display this summary
 -host val                  Use -connect instead
 -port +int                 Use -connect instead
 -connect val               TCP/IP where to connect (default is :4433)
 -bind val                  bind local address for connection
 -proxy val                 Connect to via specified proxy to the real server
 -unix val                  Connect over the specified Unix-domain socket
 -4                         Use IPv4 only
 -6                         Use IPv6 only
 -verify +int               Turn on peer certificate verification
 -cert infile               Certificate file to use, PEM format assumed
 -certform PEM|DER          Certificate format (PEM or DER) PEM default
 -nameopt val               Various certificate name options
 -key val                   Private key file to use, if not in -cert file
 -keyform PEM|DER|ENGINE    Key format (PEM, DER or engine) PEM default
 -pass val                  Private key file pass phrase source
 -CApath dir                PEM format directory of CA's
 -CAfile infile             PEM format file of CA's
 -no-CAfile                 Do not load the default certificates file
 -no-CApath                 Do not load certificates from the default certificates directory
 -requestCAfile infile      PEM format file of CA names to send to the server
 -dane_tlsa_domain val      DANE TLSA base domain
 -dane_tlsa_rrdata val      DANE TLSA rrdata presentation form
 -dane_ee_no_namechecks     Disable name checks when matching DANE-EE(3) TLSA records
 -reconnect                 Drop and re-make the connection with the same Session-ID
 -showcerts                 Show all certificates sent by the server
 -debug                     Extra output
 -msg                       Show protocol messages
 -msgfile outfile           File to send output of -msg or -trace, instead of stdout
 -nbio_test                 More ssl protocol testing
 -state                     Print the ssl states
 -crlf                      Convert LF from terminal into CRLF
 -quiet                     No s_client output
 -ign_eof                   Ignore input eof (default when -quiet)
 -no_ign_eof                Don't ignore input eof
 -starttls val              Use the appropriate STARTTLS command before starting TLS
 -xmpphost val              Alias of -name option for "-starttls xmpp[-server]"
 -rand val                  Load the file(s) into the random number generator
 -writerand outfile         Write random data to the specified file
 -sess_out outfile          File to write SSL session to
 -sess_in infile            File to read SSL session from
 -use_srtp val              Offer SRTP key management with a colon-separated profile list
 -keymatexport val          Export keying material using label
 -keymatexportlen +int      Export len bytes of keying material (default 20)
 -maxfraglen +int           Enable Maximum Fragment Length Negotiation (len values: 512, 1024, 2048 and 4096)
 -fallback_scsv             Send the fallback SCSV
 -name val                  Hostname to use for "-starttls lmtp", "-starttls smtp" or "-starttls xmpp[-server]"
 -CRL infile                CRL file to use
 -crl_download              Download CRL from distribution points
 -CRLform PEM|DER           CRL format (PEM or DER) PEM is default
 -verify_return_error       Close connection on verification error
 -verify_quiet              Restrict verify output to errors
 -brief                     Restrict output to brief summary of connection parameters
 -prexit                    Print session information when the program exits
 -security_debug            Enable security debug messages
 -security_debug_verbose    Output more security debug output
 -cert_chain infile         Certificate chain file (in PEM format)
 -chainCApath dir           Use dir as certificate store path to build CA certificate chain
 -verifyCApath dir          Use dir as certificate store path to verify CA certificate
 -build_chain               Build certificate chain
 -chainCAfile infile        CA file for certificate chain (PEM format)
 -verifyCAfile infile       CA file for certificate verification (PEM format)
 -nocommands                Do not use interactive command letters
 -servername val            Set TLS extension servername (SNI) in ClientHello (default)
 -noservername              Do not send the server name (SNI) extension in the ClientHello
 -tlsextdebug               Hex dump of all TLS extensions received
 -status                    Request certificate status from server
 -serverinfo val            types  Send empty ClientHello extensions (comma-separated numbers)
 -alpn val                  Enable ALPN extension, considering named protocols supported (comma-separated list)
 -async                     Support asynchronous operation
 -ssl_config val            Use specified configuration file
 -max_send_frag +int        Maximum Size of send frames
 -split_send_frag +int      Size used to split data for encrypt pipelines
 -max_pipelines +int        Maximum number of encrypt/decrypt pipelines to be used
 -read_buf +int             Default read buffer size to be used for connections
 -no_ssl3                   Just disable SSLv3
 -no_tls1                   Just disable TLSv1
 -no_tls1_1                 Just disable TLSv1.1
 -no_tls1_2                 Just disable TLSv1.2
 -no_tls1_3                 Just disable TLSv1.3
 -bugs                      Turn on SSL bug compatibility
 -no_comp                   Disable SSL/TLS compression (default)
 -comp                      Use SSL/TLS-level compression
 -no_ticket                 Disable use of TLS session tickets
 -serverpref                Use server's cipher preferences
 -legacy_renegotiation      Enable use of legacy renegotiation (dangerous)
 -no_renegotiation          Disable all renegotiation.
 -legacy_server_connect     Allow initial connection to servers that don't support RI
 -no_resumption_on_reneg    Disallow session resumption on renegotiation
 -no_legacy_server_connect  Disallow initial connection to servers that don't support RI
 -allow_no_dhe_kex          In TLSv1.3 allow non-(ec)dhe based key exchange on resumption
 -prioritize_chacha         Prioritize ChaCha ciphers when preferred by clients
 -strict                    Enforce strict certificate checks as per TLS standard
 -sigalgs val               Signature algorithms to support (colon-separated list)
 -client_sigalgs val        Signature algorithms to support for client certificate authentication (colon-separated list)
 -groups val                Groups to advertise (colon-separated list)
 -curves val                Groups to advertise (colon-separated list)
 -named_curve val           Elliptic curve used for ECDHE (server-side only)
 -cipher val                Specify TLSv1.2 and below cipher list to be used
 -ciphersuites val          Specify TLSv1.3 ciphersuites to be used
 -min_protocol val          Specify the minimum protocol version to be used
 -max_protocol val          Specify the maximum protocol version to be used
 -record_padding val        Block size to pad TLS 1.3 records to.
 -debug_broken_protocol     Perform all sorts of protocol violations for testing purposes
 -no_middlebox              Disable TLSv1.3 middlebox compat mode
 -policy val                adds policy to the acceptable policy set
 -purpose val               certificate chain purpose
 -verify_name val           verification policy name
 -verify_depth int          chain depth limit
 -auth_level int            chain authentication security level
 -attime intmax             verification epoch time
 -verify_hostname val       expected peer hostname
 -verify_email val          expected peer email
 -verify_ip val             expected peer IP address
 -ignore_critical           permit unhandled critical extensions
 -issuer_checks             (deprecated)
 -crl_check                 check leaf certificate revocation
 -crl_check_all             check full chain revocation
 -policy_check              perform rfc5280 policy checks
 -explicit_policy           set policy variable require-explicit-policy
 -inhibit_any               set policy variable inhibit-any-policy
 -inhibit_map               set policy variable inhibit-policy-mapping
 -x509_strict               disable certificate compatibility work-arounds
 -extended_crl              enable extended CRL features
 -use_deltas                use delta CRLs
 -policy_print              print policy processing diagnostics
 -check_ss_sig              check root CA self-signatures
 -trusted_first             search trust store first (default)
 -suiteB_128_only           Suite B 128-bit-only mode
 -suiteB_128                Suite B 128-bit mode allowing 192-bit algorithms
 -suiteB_192                Suite B 192-bit-only mode
 -partial_chain             accept chains anchored by intermediate trust-store CAs
 -no_alt_chains             (deprecated)
 -no_check_time             ignore certificate validity time
 -allow_proxy_certs         allow the use of proxy certificates
 -xkey infile               key for Extended certificates
 -xcert infile              cert for Extended certificates
 -xchain infile             chain for Extended certificates
 -xchain_build              build certificate chain for the extended certificates
 -xcertform PEM|DER         format of Extended certificate (PEM or DER) PEM default
 -xkeyform PEM|DER          format of Extended certificate's key (PEM or DER) PEM default
 -tls1                      Just use TLSv1
 -tls1_1                    Just use TLSv1.1
 -tls1_2                    Just use TLSv1.2
 -tls1_3                    Just use TLSv1.3
 -dtls                      Use any version of DTLS
 -timeout                   Enable send/receive timeout on DTLS connections
 -mtu +int                  Set the link layer MTU
 -dtls1                     Just use DTLSv1
 -dtls1_2                   Just use DTLSv1.2
 -nbio                      Use non-blocking IO
 -psk_identity val          PSK identity
 -psk val                   PSK in hex (without 0x)
 -psk_session infile        File to read PSK SSL session from
 -srpuser val               SRP authentication for 'user'
 -srppass val               Password for 'user'
 -srp_lateuser              SRP username into second ClientHello message
 -srp_moregroups            Tolerate other than the known g N values.
 -srp_strength +int         Minimal length in bits for N
 -nextprotoneg val          Enable NPN extension, considering named protocols supported (comma-separated list)
 -engine val                Use engine, possibly a hardware device
 -ssl_client_engine val     Specify engine to be used for client certificate operations
 -ct                        Request and parse SCTs (also enables OCSP stapling)
 -noct                      Do not request or parse SCTs (default)
 -ctlogfile infile          CT log list CONF file
 -keylogfile outfile        Write TLS secrets to file
 -early_data infile         File to send as early data
 -enable_pha                Enable post-handshake-authentication
```

