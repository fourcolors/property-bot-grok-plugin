# Connection and verification

The connector uses Streamable HTTP at https://mcp.property.bot/mcp. Let the host
manage OAuth access tokens and refresh. Discover authentication through
https://mcp.property.bot/.well-known/oauth-protected-resource/mcp.
The site's `/login` cookie is for operators; it does not authorize MCP.

If sign-in is missing or expired, use the connector's authentication action and
let the user finish in their browser. Never ask for access tokens or passwords
in chat, copy credentials into files, or fall back to trusted-runtime headers.
If the host cannot complete discovery or authentication, report the exact stage
that failed without tokens, cookies, or personal data. Offer the human route
from https://property.bot/auth.md while the connector is unavailable.

## Link a phone

Only start this flow after `phone_verification_required` or an explicit request
to link a phone. Linking can associate existing housing records with this account.

1. Before sending any code, confirm that this host can collect a secret outside
   ordinary chat and pass it directly to `confirm_phone_verification`. A generic
   password vault is not proof of that capability. If support is unavailable or
   unknown, stop before sending and explain that phone linking needs a supported
   client. Do not claim the account is linked.
2. Ask for the user's own phone number and their preferred `sms` or `whatsapp`
   delivery channel. Explain that the next action sends a verification code.
   Proceed when they request that send. Current support is +1 US/Canada numbers.
3. Call `start_phone_verification` with `phone` in E.164 and `channel`.
   Read the returned `channel`, `forced_whatsapp`, and `expires_at`; a known
   WhatsApp number may receive the code there even when SMS was requested.
4. Use the host's secure input mechanism for the six-digit code if available.
   If it cannot pass the code securely to the verification tool, pause linking
   and report that secure verification needs a supported client. Do not solicit
   a code in ordinary chat, guess it, or read unrelated messages to obtain it.
5. Call `confirm_phone_verification` with `code`. After a successful result,
   retry `lookup_person` with `{}` and resume the original task.

Respect rate limits and expiry messages. Resend only at the user's request;
never loop on a rejected code or switch numbers to work around a failed link.
Phone is an input to `start_phone_verification` only. Profile and match tools
derive identity server-side and have no phone argument.
