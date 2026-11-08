<!-- LOVABLE:BEGIN -->
> [!IMPORTANT]
> This project is connected to [Lovable](https://lovable.dev). Avoid rewriting
> published git history — force pushing, or rebasing/amending/squashing commits
> that are already pushed — as it rewrites history on Lovable's side and the
> user will likely lose their project history.
>
> Commits you push to the connected branch sync back to Lovable and show up in
> the editor, so keep the branch in a working state.
<!-- LOVABLE:END -->

- Products live in the `products` table; storefront reads them via public server functions in `src/lib/products.functions.ts`, admin writes via the browser client under RLS (`has_role(...,'admin')`). Why: one source of truth the owner edits from /admin.
- Admin role is auto-granted by a trigger on signup for the owner's email; never check admin client-side only. Product photos go to the private `product-images` bucket with long-lived signed URLs (public buckets are blocked in this workspace).
- Cart calculations and line creation live in a browser-safe pure module; nullable prices remain unknown and block a payable total so unconfirmed candles can be saved without being treated as free.
- Keep the cart context and hook separate from the provider component to preserve one shared context when Fast Refresh updates the provider.
- Cart identities include slug and customisation, and checkout revalidates options and prices on the server so different candle variants cannot merge or trust browser totals.
- Payment proof uploads remain private and are verified by the authenticated server before payment submission; order status review is restricted to admin roles.
- Shared account state uses one root AuthProvider subscription; sign-out cancels and clears protected queries before clearing the session. Why: prevents stale account data and duplicate auth listeners.
- Receipts render saved order snapshots fetched under order RLS, with payment confirmation separate from proof upload. Why: historical prices and payment status must not depend on the cart or current catalog.
- Payment status changes go through the `public.submit_payment_proof` security-definer function instead of a privileged backend client, so the shop runs on hosts that cannot hold a service key. Why: only the database may mark an order paid, and the rule must survive being called under the customer's own session.
- Netlify is the only external deploy target, selected when `NETLIFY` is set in `vite.config.ts`; `scripts/check-netlify-output.mjs` (postbuild) fails a Netlify build that produced no server handler or no photos. Why: Lovable's own build output stays untouched and a mis-built deploy fails loudly instead of serving "Page not found".
- Login destinations depend on authenticated user_roles reads, never email matching or browser storage. Why: owner entry must preserve server-enforced privileges.
