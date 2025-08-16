# Shopify Webhook Integration Setup Guide

This guide will help you set up Shopify webhook integration to automatically receive order data when orders are created or updated in your Shopify store.

## 🚀 **Benefits of Webhook Integration**

- **Real-time data**: Orders are automatically sent to your app when created/updated
- **No API polling**: No need to repeatedly check for new orders
- **Efficient**: Only processes orders when they actually change
- **Reliable**: Shopify handles retry logic for failed webhook deliveries
- **Scalable**: Works with any number of orders without performance impact

## 📋 **Prerequisites**

- A Shopify store (paid plan required for API access)
- Admin access to your Shopify store
- A publicly accessible HTTPS endpoint for webhooks
- Basic understanding of webhooks and API integrations

## 🔧 **Step 1: Generate Webhook Secret**

1. **Generate a Random Secret**
   ```bash
   # On Linux/Mac (generate 32 random characters)
   openssl rand -hex 16
   
   # Or use an online generator
   # Visit: https://www.random.org/strings/
   # Generate: 32 characters, alphanumeric
   ```

2. **Example Secret**
   ```
   a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6
   ```

## 🛍️ **Step 2: Create Shopify Private App**

1. **Login to Shopify Admin**
   - Go to your Shopify admin panel
   - Navigate to **Apps** → **Manage private apps**

2. **Create New Private App**
   - Click **Create a new private app**
   - Give it a name (e.g., "Cushion Order Verifier Webhook")
   - Add your email as the developer email

3. **Configure App Permissions**
   - **Orders**: Set to **Read access**
   - **Products**: Set to **Read access**
   - **Customers**: Set to **Read access** (optional, for customer information)
   - **Inventory**: Set to **No access** (not needed for order verification)

4. **Save and Generate Access Token**
   - Click **Save**
   - Copy the **Admin API access token** (you'll need this for the `.env` file)

## 🌐 **Step 3: Make Your App Publicly Accessible**

### Option A: Local Development with ngrok
1. **Install ngrok**
   ```bash
   # Download from https://ngrok.com/download
   # Or use package manager
   npm install -g ngrok
   ```

2. **Start your FastAPI app**
   ```bash
   python main.py
   ```

3. **Create ngrok tunnel**
   ```bash
   ngrok http 8000
   ```

4. **Copy the HTTPS URL**
   ```
   https://abc123.ngrok.io
   ```

### Option B: Production Deployment
- Deploy to a cloud service (Heroku, AWS, DigitalOcean, etc.)
- Ensure HTTPS is enabled
- Use your production domain

## ⚙️ **Step 4: Configure Environment Variables**

1. **Update Your `.env` File**
   ```bash
   # OpenAI Configuration
   OPENAI_API_KEY=sk-your-openai-key-here
   
   # Shopify Configuration
   SHOPIFY_SHOP_NAME=yourstorename
   SHOPIFY_ACCESS_TOKEN=shpca_your_token_here
   SHOPIFY_API_VERSION=2023-10
   SHOPIFY_WEBHOOK_SECRET=a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6
   ```

2. **Update Webhook URL in Code**
   In `main.py`, update the webhook URL:
   ```python
   webhook_url = f"https://your-domain.com/shopify/webhook/orders"
   # Replace with your actual domain or ngrok URL
   ```

## 🔗 **Step 5: Set Up Webhooks**

1. **Start Your Application**
   ```bash
   python main.py
   ```

2. **Set Up Webhooks Manually**
   - Visit: `http://localhost:8000/shopify/webhooks/setup`
   - Or use the button in the web interface

3. **Verify Webhook Creation**
   - Visit: `http://localhost:8000/shopify/webhooks/status`
   - Check that webhooks are configured correctly

## 🧪 **Step 6: Test the Integration**

1. **Create a Test Order in Shopify**
   - Go to your Shopify admin → Orders
   - Create a new test order
   - Or use an existing order

2. **Check Webhook Reception**
   - Visit: `http://localhost:8000/shopify/webhooks/status`
   - Look for the order ID in the cached orders list

3. **Test Order Verification**
   - In the web interface, select "Pull from Shopify"
   - Enter the order ID from step 1
   - Click "Verify Order"

## 📊 **Webhook Endpoints**

Your app now provides these endpoints:

- `POST /shopify/webhook/orders` - Receives Shopify order webhooks
- `GET /shopify/webhooks/setup` - Manually set up webhooks
- `GET /shopify/webhooks/status` - Check webhook status and cached orders
- `GET /shopify/orders/{order_id}` - Get specific order from cache

## 🔍 **Monitoring Webhooks**

### Check Webhook Status
```bash
curl http://localhost:8000/shopify/webhooks/status
```

### View Cached Orders
The status endpoint shows:
- Webhook configuration status
- Number of cached orders
- List of available order IDs

### Application Logs
Check `app.log` for webhook activity:
```
2025-01-14 12:00:00 - INFO - Received Shopify webhook: orders/create
2025-01-14 12:00:01 - INFO - Successfully processed webhook order: 1234567890
```

## 🚨 **Troubleshooting**

### Common Issues

1. **"Webhook not received"**
   - Check that your app is publicly accessible
   - Verify HTTPS is enabled
   - Check Shopify admin for webhook delivery status

2. **"Invalid webhook signature"**
   - Verify `SHOPIFY_WEBHOOK_SECRET` is correct
   - Check that the secret matches what Shopify is sending

3. **"Order not found in cache"**
   - Ensure the order was created after webhook setup
   - Check webhook status for cached orders
   - Verify webhook is receiving data

4. **"Webhook setup failed"**
   - Check Shopify credentials
   - Verify app permissions
   - Check application logs for detailed errors

### Debug Steps

1. **Check Webhook Status**
   ```bash
   curl http://localhost:8000/shopify/webhooks/status
   ```

2. **Review Application Logs**
   ```bash
   tail -f app.log | grep -i webhook
   ```

3. **Test Webhook Endpoint**
   ```bash
   curl -X POST http://localhost:8000/shopify/webhook/orders \
     -H "Content-Type: application/json" \
     -d '{"test": "data"}'
   ```

## 🔒 **Security Best Practices**

1. **Webhook Verification**
   - Always verify webhook signatures using HMAC
   - Never trust unverified webhook data
   - Use a strong, random webhook secret

2. **HTTPS Only**
   - Webhooks must use HTTPS in production
   - Shopify will not send webhooks to HTTP endpoints

3. **Access Control**
   - Limit webhook endpoint access
   - Monitor webhook usage and patterns
   - Rotate webhook secrets periodically

## 📈 **Production Considerations**

1. **Database Storage**
   - Replace in-memory cache with database storage
   - Implement proper data persistence
   - Add data retention policies

2. **Error Handling**
   - Implement webhook retry logic
   - Add dead letter queues for failed webhooks
   - Monitor webhook delivery success rates

3. **Scaling**
   - Use message queues for high-volume webhooks
   - Implement horizontal scaling
   - Add load balancing for webhook endpoints

## 🎯 **Next Steps**

Once webhook integration is working:

1. **Monitor Performance**
   - Track webhook delivery times
   - Monitor order processing speed
   - Optimize based on usage patterns

2. **Expand Webhook Topics**
   - Add product update webhooks
   - Include customer creation webhooks
   - Monitor inventory changes

3. **Enhance Error Handling**
   - Implement webhook retry mechanisms
   - Add comprehensive logging
   - Create alerting for webhook failures

## 📞 **Support**

If you encounter issues:

1. Check the application logs for detailed error messages
2. Verify your Shopify credentials and webhook secret
3. Test with a simple webhook first
4. Ensure your app is publicly accessible with HTTPS
5. Check Shopify admin for webhook delivery status

## 🔄 **Webhook Lifecycle**

1. **Order Created/Updated** in Shopify
2. **Shopify Sends Webhook** to your endpoint
3. **App Verifies Webhook** signature
4. **Order Data Processed** and cached
5. **Order Available** for verification in the UI

This webhook-based approach provides real-time, efficient order data synchronization between Shopify and your verification system.
