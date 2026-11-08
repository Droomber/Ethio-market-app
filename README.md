# EthioMarket — full learning project

Frontend: HTML/CSS/vanilla JavaScript. Backend: Node.js + Express. Database: MongoDB/Mongoose. Authentication: JWT + bcrypt. Contact: Nodemailer SMTP. Payment: Chapa hosted checkout v2 integration (test key only).

## 1. Requirements
Install Node.js LTS and MongoDB (local) or create a MongoDB Atlas database. Open this folder in VS Code.

## 2. Configure backend
```bash
cd backend
npm install
```
Copy `.env.example` to `.env`, then set `MONGODB_URI`, a long unique `JWT_SECRET`, `CLIENT_ORIGIN`, and `FRONTEND_URL`.
Start:
```bash
npm run dev
```
Health endpoint: `http://localhost:5000/api/health`

## 3. Seed sample products
In another terminal:
```bash
cd backend
npm run seed
```
If seed fails, inspect the terminal and verify MongoDB is running and the URI is correct.

## 4. Run frontend
Use VS Code Live Server to open `frontend/index.html` (usually `http://127.0.0.1:5500`). The API default in `frontend/js/app.js` is `http://localhost:5000/api`.

## 5. Admin dashboard and sample catalog
The admin page is `frontend/admin.html`. It now has a separate admin sign-in screen, overview cards, product search/category filtering, add/edit/delete product tools, and order-status controls. Run `npm run seed` to add the 50 sample catalog items across Electronics, Fashion, Home & Living, Beauty & Personal Care, Books & Stationery, Sports & Outdoors, Groceries and Accessories. The seed script only inserts missing sample products; it does not delete your existing catalog.

## 6. Create an administrator safely
Register a normal account from `register.html`. Then, in your own MongoDB database, find that user document in the `users` collection and change its `role` field from `customer` to `admin`. Log out and log in again. Never expose an endpoint that lets public users grant themselves admin access.

## 7. Contact email
Set `SMTP_HOST`, `SMTP_PORT`, `SMTP_SECURE`, `SMTP_USER`, `SMTP_PASS`, and `CONTACT_TO` in backend `.env`. Use your email provider's supported SMTP credentials. Keep credentials on the backend only. The form is rate-limited.

## 8. Chapa payment integration (TEST MODE)
The app creates an order, asks the backend to initialize Chapa hosted checkout, then redirects the customer to Chapa. The backend verifies payment status before marking an order paid. Follow Chapa's current official setup and sandbox instructions:
- https://docs.chapa.global/docs/v2/getting-started
- https://docs.chapa.global/docs/v2/integrations/accept-payment
- https://docs.chapa.global/docs/v2/integrations/verify-payment

Add your Chapa TEST private key to `CHAPA_PRIVATE_KEY` in backend `.env`. Never place the private key in frontend JavaScript or GitHub. This integration expects the v2 hosted-checkout API. Test keys and merchant eligibility are managed by Chapa. Do not switch to live mode for a school demonstration. The backend converts the ETB amount to integer cents as described by the current v2 documentation; verify the provider's current amount conventions in your merchant dashboard before any real use.

Payment provider redirects may include different reference query names depending on provider configuration. The verification page accepts `reference` or `ref`; if your Chapa dashboard sends a different field, adapt it to the documented response and always verify server-side. A redirect is not proof of payment.

## 9. Deployment outline
1. Push source to a private or public GitHub repository, but never commit `.env`.
2. Create a MongoDB Atlas database and allow only the required network access.
3. Deploy backend as a Node web service. Set all backend environment variables in the host dashboard, including `MONGODB_URI`, `JWT_SECRET`, `CLIENT_ORIGIN`, `FRONTEND_URL`, SMTP values and Chapa TEST key.
4. Deploy `frontend/` as a static site. Change `API` in `frontend/js/app.js` to `https://YOUR-BACKEND-HOST/api`.
5. Set `CLIENT_ORIGIN` and `FRONTEND_URL` to the exact deployed frontend URL, redeploy, and test from a separate device/network.
6. Confirm health endpoint, registration/login, product display, contact email, order creation, and payment verification.

## 10. Important production gaps
This is a substantial learning starter, not a fully audited production store. Before real commerce, add atomic stock reservation and order transactions, webhook signature verification and replay protection, robust payment reference matching, idempotent payment retries, secure session strategy, email verification/password reset, stronger schema validation, image upload/storage, admin audit logs, order cancellation/refund workflows, privacy/terms/returns/shipping policies, accessibility testing, automated tests, HTTPS, monitoring and backups. Do not fulfil orders from a browser redirect alone.


### Rich product catalog update
The demo seed file defines 50 products with ETB prices, image URLs, category, stock, brand, product code, full details, highlights, color/variant and warranty/return notes. Run `npm run seed` after updating the project. Existing products are not overwritten; for existing demo entries, delete the old demo products first if you want the newly-added descriptive fields populated, then run the seed script again. Product images load from Unsplash over the internet; an image placeholder appears if a remote image cannot load. Admins can add an image by entering a public image URL and see a preview before saving.
