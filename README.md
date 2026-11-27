# BOLO — Your Voice Business Assistant

**Speak. Manage. Grow.** BOLO is a voice-first business management prototype for small shopkeepers, street vendors and local businesses.

It is designed to make everyday record-keeping simpler through natural voice commands, with English, Hindi and Kannada language options.

## What BOLO does

- **Voice assistant:** record simple sales and expenses by speaking.
- **Sales and expenses:** add transactions and view totals.
- **Profit summary:** calculate estimated profit as sales minus expenses.
- **Inventory:** maintain a simple stock count.
- **Customer dues:** record pending customer payments.
- **Dashboard:** see key business figures and recent activity.
- **Language selector:** choose English, Hindi or Kannada for speech recognition.

## Tech stack

- React 18 + TypeScript
- Vite
- Lucide React icons
- Browser Speech Recognition API (where supported)
- Browser localStorage for demo persistence
- Docker + Nginx for container deployment

## Run locally

Requirements: Node.js 20+ and npm.

```bash
npm install
npm run dev
```

Open the local URL printed by Vite (usually `http://localhost:5173`).

## Production build

```bash
npm run build
npm run preview
```

The production files are generated in `dist/`.

## Run with Docker

```bash
docker build -t bolo-app .
docker run --rm -p 8080:80 bolo-app
```

Open `http://localhost:8080`.

## Example voice commands

Try short, clear commands such as:

- “I made a sale of 250 rupees”
- “Record an expense of 80 rupees”
- “Add 5 stock items”

The prototype uses browser speech recognition. Microphone access requires HTTPS or localhost, and browser/language support varies. If voice recognition is unavailable, use the manual transaction forms.

## Data and privacy

This hackathon MVP stores demo business records in the current browser using localStorage. Data is not uploaded to a server, shared between devices, or backed up. Do not enter real customer personal or financial information into this demo.

## Current limitations

This is a prototype, not production accounting software. Voice command parsing is intentionally basic. It does not include user accounts, cloud sync, GST/tax filing, payment gateway integration, bank connections, automated WhatsApp reminders, or verified accounting controls. Those would require further development, testing and appropriate security/privacy design.

## Suggested next steps

1. Conduct usability tests with shopkeepers.
2. Improve multilingual speech recognition and command understanding.
3. Add secure authentication and a backend database.
4. Add role-based access, backups and audit history.
5. Validate financial calculations and local compliance requirements.

## License

MIT — see [LICENSE](LICENSE).
