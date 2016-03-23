### Förkortningar

* OIDC = Open ID connect
* RP = Relying Party (en OIDC term, SP på SAML-språk)
* OP = OIDC provider (en till OIDC term, IdP på SAML-språk)

### Förutsättningar

* Användaren har redan ett obekräftat konto hos en IdP i federationen
* Användaren har möjlighet att fysiskt träffa ett RA-ombud

### Övergripande process

[[/wiki/proofing-skiss.png|align=center]]

1. Användaren loggar in hos sin IdP och väljer att initiera id-proofing

2. IdP:n startar en proofing-session för användaren, med ett unikt ID. Detta ID
   kallas "nonce" och skall vara unikt samt omöjligt för någon annan än IdP:n att
   förutsäga.

   Meningen med id:t unikhet är att det inte ska vara möjligt att förutse på ett
   sådant sätt att det blir möjligt att försöka "kvittera ut" identiteter för
   användare och sedan skapa konton hos IdP:er i de fall det lyckas.

   Om ett slump-nonce genereras bör det vara minst 128 bitar långt.

   IdP:n måste komma ihåg vilket credential som användes vid initieringen av
   proofingen. Detta för att då kan det "bevisas" att användaren innehar ett
   visst token/lösenord, efter utförd proofing.

3. IdP:ns se-leg RP-komponent gör en POST (Signed HTTP) till se-leg OP:ns authorization\_endpoint. Denna authn\_request innehåller nonce från steg 2.

4. IdP:n ger nonce till användaren, exempelvis genom att visa det i form av en QR-kod. Det är fullt möjligt att använda någon annan teknik för att berätta nonce för användaren, exempelvis:

   * QR-kod på papper eller mobil
   * Referens (användaren uppger IdPns namn och sitt personnummer)
   * Skicka in i förväg via Internet
   * NFC/QR-kod från användarens smartphone (som har en app från IdP:n)

5. Användaren går till RA-ombudet och visar upp QR-koden samt en ID-handling för vilken det finns en proofing-process:

  * [Svenskt körkort](wiki/proofing/svensktkorkort)
  * [Nationellt ID-kort](wiki/proofing/nationelltidkort)
  * ...

6. RA-ombudet genererar ett påstående om den utförda proofingen. Påståendet signeras av RA-ombudet och skickas till se-leg OP:n. Data som skickas är ett ID token
med:
  * nonce
  * vilken process som använts
  * identitet enligt denna process

7. se-leg OP:n loggar beviset för proofingen

8. se-leg OP:n genererar ett stabilt publikt subject (en unik identifierare för denna identitet) och skickar sedan ett authn_response till RP:n (ett svar på requested från steg 3) innehållandes:

  * nonce
  * vilken process som använts
  * identitet enligt denna process
  * det stabila publika subjectet
