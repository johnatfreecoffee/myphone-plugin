# MyPhone

MyPhone gives an AI agent its own phone number for calls and texts. The agent can place and answer calls, send and receive texts and pictures, read voicemail and recordings, and change how the line answers.

This plugin is the Cursor and Grok Bot connector. It points at the hosted MyPhone MCP server. It does not bundle a server, a binary, or a secret.

A MyPhone account billed on myphone.host is required. This plugin does not create accounts. An account with no active line gets: `No active MyPhone line. Manage your account at myphone.host`.

- Terms: https://myphone.host/terms
- Privacy: https://myphone.host/privacy

## Sign-in

Cursor or Grok Bot starts OAuth when the server is added. The browser opens MyPhone. Sign in with the mobile number on the account, then the 6-digit code from the text. A new account also asks for a name. Then choose Allow.

The only scope is `myphone`. There is no API key. This repository has no secrets. Dynamic client registration uses no client secret.

## Tools

`tools/list` returns these tools after sign-in. Descriptions below are the ones the server sends.

| Tool | What it does |
| --- | --- |
| `get_signup_status` | Start here after Authorize, and after any sign-in. Returns current_step and its card in ui. Saved choices show the phone as the last 4 digits only. No card data. |
| `apply_promo_code` | Manage the account at myphone.host. |
| `accept_terms` | Read https://myphone.host/terms and https://myphone.host/privacy to the person and get an explicit yes. Pass agree true only after that yes. |
| `whoami` | Who is signed in, including their number and voice. Their own phone shows as the last 4 digits only. |
| `get_pricing` | Manage the account at myphone.host. |
| `check_plan_fit` | Manage the account at myphone.host. |
| `set_auto_recharge` | Turn automatic refill on or off. Manage the account at myphone.host. |
| `search_numbers` | Search US numbers. Ask what area code they want. Returns 5 numbers plus a choice ui hint. Pass offset from next_offset when they say show me more. Read-only. Do not invent numbers. |
| `purchase_number` | Use a number the person picked from search_numbers. Pass confirmed true only after they confirm. Nothing is held before that. |
| `list_voices` | The five standard Grok voices: ara, rex, sal, eve, leo. |
| `choose_voice` | Set the voice that answers this customer's number. Standard id only: ara, rex, sal, eve, or leo. |
| `get_balance` | Read the line status for the signed-in account. |
| `top_up` | Manage the account at myphone.host. |
| `save_email` | Save the email for receipts. Ask: What's your email? Call it with what they type. |
| `save_card` | Manage the account at myphone.host. |
| `change_card` | Manage the account at myphone.host. |
| `switch_plan` | Change the line setup after the person confirms. Manage the account at myphone.host. |
| `remove_number` | Release a MyPhone number. |
| `list_usage` | List minute, text, and balance activity. |
| `list_charges` | List account activity for this line. |
| `set_call_handling` | Set how a number answers: grok_answers (Grok Bot talks), forward (ring their phone about 20 seconds, then voicemail), or voicemail. Their phone is the verified mobile from sign-in. The greeting and voice default to what they picked. |
| `set_persona` | Set the name, voice, instructions, greeting, and voicemail. choice customize returns the form and does not save. Call again with persona_name, greeting, and voicemail to save and continue. choice skip keeps the defaults. Voice is ara, rex, sal, eve, or leo. |
| `get_settings` | The current assistant and call handling for a number: assistant_name, voice, greeting, voicemail, instructions, tone (casual, friendly, professional, or auto), call_mode (grok_answers, forward, or voicemail), the last 4 of the phone that rings, agent_call, passcode_set, trusted_numbers, three_way, and contacts. It never includes the passcode. auto is casual for a shop and professional for a clinic, bank, law office, or government office. The person can also change these at https://myphone.host/account under Account settings, Assistant, so read this before you talk about or change them. Never rely on earlier values. Never ask for the voice passcode in chat. |
| `update_settings` | Change any of the assistant or call handling settings once the number is set up. Pass only what the person wants changed; everything else stays. The next call uses the new settings. Fields: assistant_name, voice (ara, rex, sal, eve, or leo), greeting (what the assistant says when it answers), voicemail (the voicemail greeting), instructions (what the assistant should know or do on calls), tone (casual, friendly, professional, or auto), call_mode (grok_answers = the assistant answers, forward = ring their phone about 20 seconds then voicemail, voicemail = straight to voicemail), forward_number (their own phone), three_way (true or false). Three-way calls need a voice passcode set on the account page. Never ask for that passcode in chat. auto is casual for a shop and professional for a clinic, bank, law office, or government office. Read back what changed. |
| `save_contact` | Save a person they may add to a three-way call. Pass name and phone. This does not dial. |
| `list_contacts` | The people saved for three-way calls: name and phone. |
| `remove_contact` | Remove a saved contact. Pass the id from list_contacts. |
| `add_to_call` | Dial a saved contact, or a phone number, into the person's live three-way call. Pass call_id and who. The voice passcode must already be accepted on that call. |
| `join_call` | Bring their assistant into a live call they are already on. Pass call_id. The assistant asks for the voice passcode on the call before it helps. Never ask for the passcode in chat. |
| `set_bot_callback` | Register the https webhook for inbound texts, warm-transfer decisions, outbound call results, hold check-ins, and phone-menu stops. MyPhone generates the HMAC secret and returns it once. STOP, HELP, and opt-out stay with MyPhone. |
| `get_pending_events` | Pull texts, transfer decisions, call results, hold check-ins, and phone-menu stops when the webhook is not used. Each event is returned once and marked delivered. respond_event still answers a text, a transfer, or a hold check-in. A call_result is one per call and means the call ended. A hold_checkin means an outbound call has been on hold for about 2 minutes. An ivr_notice means the voice stopped pressing keys because the menu repeated or the key limit was hit. Scoped to this account. |
| `respond_event` | Answer one pending event. Pass reply for a text, or decision accept or decline for a transfer. For a call_result or an ivr_notice, pass event_id to mark it seen. For a hold_checkin, pass action keep_holding or hang_up. Opt-out still blocks a text. |
| `hold_decision` | Answer a hold check-in on an outbound call. action is keep_holding or hang_up. Pass event_id from the hold_checkin event when you have it. keep_holding stays on the line and asks again later. hang_up ends the call even when no hold check-in is waiting. Pass call_id to end that call. This is always on, not a setting. |
| `hang_up` | End a live call now. Pass call_id from call_number, or call_sid. Works on hold, in a menu, or in conversation. A hold check-in is not required. end_call is the same tool. |
| `end_call` | Alias of hang_up. End a live call now by call_id or call_sid. A hold check-in is not required. |
| `send_instruction` | Send one line into your own live call. Pass call_id and text. Server rules still win. It does not work on someone else's call. |
| `press_keys` | Press phone-menu keys on your own live call. Pass call_id and digits. The server still enforces the menu, PIN, and digit-cap rules. |
| `patch_user_in` | Ring the account owner into their own live call. Pass call_id. It does not work on someone else's call. |
| `get_live_transcript` | The transcript so far for your own call. Pass call_id. Someone else's call is not returned. |
| `checkin_decision` | Answer a call_checkin. action is continue, hold, or end. The event includes the transcript so far. Pass event_id when you have it. |
| `list_active_calls` | Admin only. List live calls on MyPhone: call_id, call_sid, who is on the call, and status. |
| `end_active_call` | Admin only. End any live call. Pass call_id or call_sid from list_active_calls. |
| `call_me` | Call the person's own phone and talk live with them. Uses their mobile, or a trusted number when they name one. If Call my agent is on, the call asks for the passcode before anything else. Without confirmed true, return the confirm card and do not dial. |
| `call_number` | Place a call from the person's MyPhone number. voice is optional: ara, rex, sal, eve, or leo. tone is optional: casual, friendly, or professional. Leave it off to use their saved default, which is casual for a shop and professional for a clinic, bank, law office, or government office. style is an optional note for wording, such as a friendly Cajun accent, and it can override how the tone is worded. persona is a short length-capped note for this call. The voice waits until a person finishes greeting, says one short line, and stops if they start talking. Without confirmed true, return the confirm card and do not dial. Dial only when confirmed is true. |
| `send_sms` | Text from the person's MyPhone number. Pass to and body. To send pictures, pass media_urls (public https links) or images (base64 with mime_type). JPEG, PNG, or GIF, up to 10 pictures, 5 MB total. MyPhone will not text a person who opted out with STOP. |
| `confirm_call_text` | Send or skip a text that was requested on a call. Use only the yes or no card from get_call. Pass call_id, text_id, and send. Nothing is sent until send is true. |
| `list_calls` | List calls for this account. It does not sign recording links. Use get_call or get_recording for the audio link. Page with limit and offset. Filter with from and to dates. |
| `get_call` | One call: outcome, done, summary, transcript, duration_seconds, and recording_url when a recording is stored. from_number placed the call. to_number was called. A live call is in-progress. |
| `list_voicemails` | List voicemails for this account. Page with limit and offset. Filter with from and to dates. |
| `get_recording` | Short-lived signed download URL for one call recording or voicemail. Scoped to this account. |
| `list_sms_threads` | List text threads for this account. Page with limit and offset. Filter with from and to dates. |
| `get_sms_thread` | Messages in one thread: id, direction, from, to, body, timestamp, and media_urls (signed links to pictures, which expire). |
| `export_data` | Export calls, sms, or voicemails as json or csv for a date range. Small files come back inline. Larger files come back as a short-lived signed URL. |
| `select_plan` | Pick how this line is set up. Manage the account at myphone.host. |
| `select_topup` | Manage the account at myphone.host. |
| `get_instructions` | The newest MyPhone instructions and their version. Every MyPhone tool result has instructions_version. When it differs from the version you are following, call this once and follow what it returns. |

## Network endpoints

The plugin client calls only MyPhone:

- `POST https://myphone.host/mcp`
- `GET https://myphone.host/.well-known/oauth-protected-resource`
- `GET https://myphone.host/.well-known/oauth-protected-resource/mcp`
- `GET https://myphone.host/.well-known/oauth-authorization-server`
- `GET https://myphone.host/.well-known/oauth-authorization-server/mcp`
- `POST https://myphone.host/oauth/register`
- `GET https://myphone.host/oauth/authorize`
- `POST https://myphone.host/oauth/token`
- `POST https://myphone.host/oauth/revoke`

After a tool runs, the MyPhone server may call:

- `https://zdcnlzfewmckzzmhseng.supabase.co/rest/v1/`
- `https://zdcnlzfewmckzzmhseng.supabase.co/storage/v1/object/voicemails/`
- `https://zdcnlzfewmckzzmhseng.supabase.co/storage/v1/object/mms/`
- `https://zdcnlzfewmckzzmhseng.supabase.co/storage/v1/object/exports/`
- `https://zdcnlzfewmckzzmhseng.supabase.co/storage/v1/object/sign/`
- `POST https://sxjtpprtaascxafddddg.supabase.co/functions/v1/myphone-signup-sync`
- `https://api.twilio.com/2010-04-01/Accounts/{AccountSid}/Calls.json`
- `https://api.twilio.com/2010-04-01/Accounts/{AccountSid}/Calls/{CallSid}.json`
- `https://api.twilio.com/2010-04-01/Accounts/{AccountSid}/Calls/{CallSid}/Recordings.json`
- `https://api.twilio.com/2010-04-01/Accounts/{AccountSid}/Recordings/{RecordingSid}.json`
- `https://api.twilio.com/2010-04-01/Accounts/{AccountSid}/Messages.json`
- `https://api.twilio.com/2010-04-01/Accounts/{AccountSid}/IncomingPhoneNumbers.json`
- `https://api.twilio.com/2010-04-01/Accounts/{AccountSid}/IncomingPhoneNumbers/{PhoneSid}.json`
- `https://api.twilio.com/2010-04-01/Accounts/{AccountSid}/AvailablePhoneNumbers/US/Local.json`
- `https://api.twilio.com/2010-04-01/Accounts/{AccountSid}/AvailablePhoneNumbers/US/TollFree.json`
- `https://api.twilio.com/2010-04-01/Accounts/{AccountSid}/Conferences.json`
- `https://messaging.twilio.com/v1/Services/{MessagingServiceSid}/PhoneNumbers`
- `POST https://api.x.ai/v1/tts`
- `POST https://api.x.ai/v1/stt`
- `POST https://api.x.ai/v1/chat/completions`
- `POST https://api.resend.com/emails`
- `POST https://connect.squareup.com/v2/payments`
- `GET https://connect.squareup.com/v2/payments`
- `GET https://connect.squareup.com/v2/locations`
- `POST https://connect.squareup.com/v2/customers`
- `GET https://connect.squareup.com/v2/customers/{id}`
- `PUT https://connect.squareup.com/v2/customers/{id}`
- `POST https://connect.squareup.com/v2/cards`
- `GET https://connect.squareup.com/v2/cards`
- `GET https://connect.squareup.com/v2/cards/{id}`
- `POST https://connect.squareup.com/v2/cards/{id}/disable`
- `POST https://connect.squareup.com/v2/subscriptions`
- `GET https://connect.squareup.com/v2/subscriptions/{id}`
- `POST https://connect.squareup.com/v2/subscriptions/{id}/cancel`
- `POST https://connect.squareup.com/v2/catalog/object`
- `POST https://connect.squareup.com/v2/catalog/search`
- `GET https://connect.squareup.com/v2/online-checkout/payment-links/{id}`
- `wss://voice.myphone.host/media/{CallSid}`
- `wss://voice.myphone.host/conf/{room}`

`set_bot_callback` registers one https webhook the account owner chooses. MyPhone sends event JSON there and signs it with `X-MyPhone-Signature`.

The card resource may also load `https://myphone.host`, `https://web.squarecdn.com`, `https://pci-connect.squareup.com`, and `https://connect.squareup.com`.

## Data and permissions

The connector asks for the `myphone` scope only. Sign-in collects the mobile number, the one-time text code, and a name only when the account does not have one yet. Tool calls use that signed-in account and no other.

This repository has no dependencies.

## Logos

- `assets/logo.svg` is the MyPhone mark on a `#05070D` plate.
- `assets/logo.png` is the same mark as a 1024×1024 PNG with no transparency.

## Reviewer testing

1. Copy this repo to `~/.cursor/plugins/local/myphone` and reload Cursor. In Grok Bot, add the plugin from the marketplace once it is listed, or add the MCP server `https://myphone.host/mcp`.
2. Open the MyPhone server and finish sign-in in the browser. Use the mobile number on a MyPhone account that already has an active line. Enter the 6-digit code from the text. Allow access.
3. Ask the agent to list MyPhone tools. The tools in the table above should appear.
4. Ask who is signed in, then ask for recent calls or texts.
5. Place a call or send a text only to a number you control.
6. Sign out. The next tool call should ask you to sign in again.
7. An account with no active line returns `No active MyPhone line. Manage your account at myphone.host`.

## License

MIT. See [LICENSE](LICENSE).
