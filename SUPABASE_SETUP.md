# Supabase setup for Scriptora orders

The repository already includes a Supabase-powered order portal and admin screen in `V2`. This setup adds the database schema, access policies, private upload bucket, order IDs, and secure public order tracking that the portal expects.

## 1. Apply the database setup

1. Open the Supabase project configured in `V2`.
2. Go to **SQL Editor**, create a query, paste in [`supabase-setup.sql`](supabase-setup.sql), and run it.
3. In **Authentication → Providers**, enable Email/password sign-in. Configure email confirmation and SMTP for your launch requirements.

The portal uses the project's publishable/anon key in the browser. That key is intended to be public; security comes from the SQL policies. **Never add a Supabase `service_role` or secret key to browser code or this repository.**

## 2. Create the first admin

1. Sign up through the portal with the email account you control.
2. If email confirmation is enabled, confirm the account and sign in once so its profile is created.
3. In Supabase SQL Editor, replace the example email and run:

   ```sql
   update public.profiles
   set is_admin = true
   where email = 'you@example.com';
   ```

4. Sign out and back in. The **Admin** tab will then appear. Do not grant admin access to customer accounts.

## 3. Publish and test the order workflow

The root `index.html` is the marketing/prototype page; `V2` is the authenticated, Supabase-backed portal. Publish `V2` at a route such as `/V2` (or configure your hosting to serve it as the app entry point). Then test:

- Create a customer account and submit a small test order.
- Confirm the order appears under **My orders** and in the admin dashboard.
- Change the order status in **Admin** and verify it appears in the customer's order history/tracking.
- Upload a test PDF and verify that the admin can open it while it remains private.
- Use **Track** with the order number and the customer's phone or email.

The admin dashboard supports status updates, payment marking, order search/filtering, order-file access, and payout-request review. It does not automatically charge customers or send email/WhatsApp notifications; those require payment and messaging provider credentials plus server-side webhook/Edge Function configuration. No secrets should be added to the static page.

## Security notes

- Orders can only be created by signed-in users and must belong to that user.
- Customers can read only their own orders; only an admin can update orders or read uploaded documents.
- Public tracking returns limited status information only when both the order ID and matching customer contact are provided.
- Uploaded order files are stored in a private bucket with a 10 MB per-file limit.
