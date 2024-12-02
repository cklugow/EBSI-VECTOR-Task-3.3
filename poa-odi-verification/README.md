# PoA & ODI verification process

Our goal is to research the scenario(s) where a holder presents Power of Attorney credential to prove the legitimacy to act on behalf of a company.
While presenting the PoA VC itself will provide the validity of the credential, it is to be researched how this PoA can be linked to a specific company
via ODI credential. Only the verifiable link between PoA and ODI will ensure the PoA holder may act on behalf of a company for which the ODI has been issued.

There are two main approaches to linking Power of Attorney and Organizational Digital Identifier credentials that are being researched and implemented 
during the Business Registry use case.

## Published ODI VP

One approach is to publish ODI VP so that it is available for verifiers. The idea behind this approach is to make the ODI VP publicly available and the
verification process will be able to resolve the location of the VP using verified information from a presented PoA.

[Technical specification](rfc01_poa-odi-verification_published-odi-vp.md)

## Embedded ODI VC

On the other hand, embedding ODI VC in a PoA VC would not require the verifier to rely on external availability of the published ODI. Instead, the ODI VC
would be directly available for the verifier.

[Technical specification](rfc01_poa-odi-verification_embedded-odi-vc.md)
