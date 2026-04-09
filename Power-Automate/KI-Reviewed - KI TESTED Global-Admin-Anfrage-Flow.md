# Global Admin Anfrage – Power Automate Flow KI.Tested KI.Reviewed

## Zweck
Mitarbeitende können temporären Global Admin Zugriff beantragen. Einer von zwei definierten Vorgesetzten muss den Antrag genehmigen.

---

## Voraussetzungen

- Microsoft Forms Formular erstellt (in Teams-Kanal oder persönlich)
- Formular-Einstellung: **Nur Personen in der Organisation können antworten** + **Namen erfassen** aktiv
- Power Automate Lizenz vorhanden

---

## Flow-Aufbau

### 1. Trigger – When a new response is submitted
- Formular auswählen
- **Split On** aktiv lassen (Standard)

> ⚠️ Mit Split On liefert der Trigger nur `formId` und `responseId` – keine Formularfelder. Deshalb ist Schritt 2 zwingend.

---

### 2. Antwortdetails abrufen (Get response details)
- **Formular-ID:** `triggerBody()?['resourceData/formId']`
- **Antwort-ID:** `triggerBody()?['resourceData/responseId']`

Ab diesem Schritt stehen alle Formularfelder (Name, Begründung, Datum, Responder-E-Mail) als dynamischer Inhalt zur Verfügung.

---

### 3. Create an approval
- **Typ:** Approve/Reject – First to respond
- **Zugewiesen zu:** Die zwei Vorgesetzten (fix eintragen)
- **Titel:** z.B. `Antrag Global Admin – [Name aus Formular]`
- **Details:**
  ```
  Antragsteller: [Wie heisst du]
  Begründung: [Warum Global Admin]
  Benötigt bis: [Datum]
  ```
- **Requestor:** `outputs('Antwortdetails_abrufen')?['body/responder']`
- **Link:** Temporären PIM-Link oder Entra-Link einfügen

---

### 4. Apply to each → Post adaptive card (optional)
Teams-Benachrichtigung an die Approver. Kann weggelassen werden wenn E-Mail-Benachrichtigung reicht.

---

### 5. Wait for an approval
Wartet auf die Entscheidung eines der zwei Vorgesetzten.

---

### 6. Condition
- **Wahr (genehmigt):** Inform requestor of approval – E-Mail/Teams-Nachricht an Antragsteller + temporären Global Admin Link
- **Falsch (abgelehnt):** Inform requestor of rejection – E-Mail/Teams-Nachricht an Antragsteller

---

## Hinweise

- Recipient in den Inform-Schritten: `outputs('Antwortdetails_abrufen')?['body/responder']`
- Flow läuft unter der Verbindung des Flow-Erstellers – dieser benötigt Lesezugriff auf das Formular
<img width="2153" height="512" alt="image" src="https://github.com/user-attachments/assets/a57e232a-4e56-4268-8e4f-f0373b854994" /><img width="737" height="762" alt="image" src="https://github.com/user-attachments/assets/90fd1f9d-351f-4478-8868-4f1d0af7119c" />
<img width="833" height="840" alt="image" src="https://github.com/user-attachments/assets/8e90247d-355f-4d15-9388-4b55f3b35680" />

