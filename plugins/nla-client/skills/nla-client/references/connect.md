# Connecting a Client Agent to Not Legal Advice (one time)

This is the same flow the Not Legal Advice web page gives a Client's agent (the connect-our-agents flow). If
you have the `connect-our-agents` skill, follow it; the steps below are what it does. Not Legal Advice (NLA)
is Fulcra user id `041f5bee-f6cc-4505-8da7-ea880744aafa`. Its agent signs messages `not-legal-advice`.
Use the Fulcra CLI as `uvx --python 3.11 fulcra-api@latest <command>`.

Skip any step already done (check `share list-outgoing` and `share list-incoming`).

1. Get on Fulcra. If you cannot act for the human in Fulcra, follow https://docs.fulcradynamics.com/agent-get-started.txt
   and ask the human to finish the sign-in it gives you.
2. Create your side of the channel: a data type with base type Event named `<your agent name> with Not Legal Advice`,
   description starting `connect-our-agents channel`, with exactly these fields (sender, kind and body required):

```json
{"properties":{"sender":{"type":"string","minLength":1,"maxLength":128,"description":"Name of the agent that wrote the message"},"kind":{"type":"string","enum":["message","reply","ack"],"description":"message starts something; reply answers one; ack says you have read one"},"body":{"type":"string","minLength":1,"maxLength":4000,"description":"The message text"},"recipients":{"type":"array","minItems":1,"maxItems":32,"items":{"type":"string","minLength":1,"maxLength":128},"description":"Agent names the message is for; leave out for everyone in the connection"},"topic":{"type":"string","minLength":1,"maxLength":128,"description":"Short thread name; replies reuse it"},"in_reply_to":{"type":"string","format":"uuid","description":"The id of the message being answered or acknowledged"},"priority":{"type":"string","enum":["P1","P2","P3"],"description":"P1 urgent, P2 normal, P3 low"},"artifacts":{"type":"array","maxItems":32,"description":"Files the message points to, shared separately","items":{"type":"object","required":["path"],"properties":{"path":{"type":"string","minLength":1},"version":{"type":"string","minLength":1,"description":"File version or content hash, if it matters which one"},"owner":{"type":"string","format":"uuid","description":"Fulcra user ID of the account holding the file, if not the sender"}}}}},"required":["sender","kind","body"]}
```

```
uvx --python 3.11 fulcra-api@latest data-type create Event "<your agent name> with Not Legal Advice" \
  -d "connect-our-agents channel: <your agent name> (<human name>) with Not Legal Advice." --fields '<the JSON above>'
```
   The new channel id looks like `Event/<uuid>`.
3. Share that one data type, read-only, with NLA only. The share name must be exactly
   `connect-our-agents: <human name> with Not Legal Advice`:

```
uvx --python 3.11 fulcra-api@latest share create --name "connect-our-agents: <human name> with Not Legal Advice" \
  --data-type "Event/<your channel uuid>" --user-id 041f5bee-f6cc-4505-8da7-ea880744aafa
```
4. Introduce yourself (text on stdin):

```
uvx --python 3.11 fulcra-api@latest connection send 041f5bee-f6cc-4505-8da7-ea880744aafa --as <your agent name> --topic introduction
```
   Say who the human is, that you are their agent, and that you came from notlegaladvice.
5. Wait for the reply. NLA checks about every 5 minutes, shares its own channel back (an incoming share named
   `connect-our-agents: Not Legal Advice with <human name>`) and replies to the introduction. Check every 2 minutes for up to
   30 minutes with the `inbox` subcommand of this plugin's `scripts/nla_client.py`. Show the reply to the human verbatim.
6. The human can sign in at https://notlegaladvice.my.fulcra.ai/ledger to see the ledger of the exchange.

Use the same agent name every time (`--as`); the script remembers it.

A Request needs an Engagement, which only the firm administrator creates. A sender with no Engagement gets
`nla/2 rejected` with code `no_engagement`; tell the human to ask NLA on the channel (plain text, no `nla/2` prefix) how to
become a Client.
