# Shopify Integration Setup Guide

This guide will help you set up Shopify integration to automatically pull order details into the Cushion Order Verifier UI.

## Prerequisites

- A Shopify store (paid plan required for API access)
- Admin access to your Shopify store
- Basic understanding of API integrations

## Step 1: Create a Private App in Shopify

1. **Login to Shopify Admin**
   - Go to your Shopify admin panel
   - Navigate to **Apps** → **Manage private apps**

2. **Create New Private App**
   - Click **Create a new private app**
   - Give it a name (e.g., "Cushion Order Verifier")
   - Add your email as the developer email

3. **Configure App Permissions**
   - **Orders**: Set to **Read access**
   - **Products**: Set to **Read access**
   - **Customers**: Set to **Read access** (optional, for customer information)
   - **Inventory**: Set to **No access** (not needed for order verification)

4. **Save and Generate Access Token**
   - Click **Save**
   - Copy the **Admin API access token** (you'll need this for the `.env` file)

## Step 2: Get Your Shop Name

1. **Find Your Shop Name**
   - In your Shopify admin, look at the URL
   - It will be: `https://yourstorename.myshopify.com`
   - Your shop name is `yourstorename` (without `.myshopify.com`)

## Step 3: Configure Environment Variables

1. **Update Your `.env` File**
   Add these lines to your `.env` file:
   ```bash
   SHOPIFY_SHOP_NAME=yourstorename
   SHOPIFY_ACCESS_TOKEN=your_access_token_here
   SHOPIFY_API_VERSION=2023-10
   ```

2. **Example `.env` File**
   ```bash
   # OpenAI Configuration
   OPENAI_API_KEY=sk-your-openai-key-here
   
   # Shopify Configuration
   SHOPIFY_SHOP_NAME=mystore
   SHOPIFY_ACCESS_TOKEN=shpca_your_token_here
   SHOPIFY_API_VERSION=2023-10
   ```

## Step 4: Test the Integration

1. **Restart Your Application**
   ```bash
   python main.py
   ```

2. **Test with a Real Order ID**
   - Go to your Shopify admin → Orders
   - Find an order and copy its Order ID (the number, not the order number)
   - In the app, select "Pull from Shopify" and enter the Order ID
   - Click "Verify Order"

## Finding Order IDs in Shopify

### Method 1: From Admin Panel
1. Go to **Orders** in your Shopify admin
2. Click on any order
3. Look at the URL: `https://yourstore.myshopify.com/admin/orders/1234567890`
4. The number at the end is the Order ID: `1234567890`

### Method 2: From Order Details
1. Open any order in your admin
2. Look for **Order ID** in the order information
3. This is the numeric ID you need

### Method 3: From Order Export
1. Go to **Orders** → **Export**
2. Export orders as CSV
3. Look for the **Order ID** column

## Troubleshooting

### Common Issues

1. **"Shopify credentials not configured"**
   - Check that `SHOPIFY_SHOP_NAME` and `SHOPIFY_ACCESS_TOKEN` are in your `.env` file
   - Restart the application after updating `.env`

2. **"Order not found in Shopify"**
   - Verify the Order ID exists in your store
   - Make sure you're using the Order ID, not the Order Number
   - Check that the order is not archived or deleted

3. **"Unauthorized access to Shopify API"**
   - Verify your access token is correct
   - Check that the private app is still active
   - Ensure the app hasn't been deleted or deactivated

4. **"Forbidden access to Shopify API"**
   - Check your private app permissions
   - Ensure you have **Read access** to Orders and Products
   - Try recreating the private app with proper permissions

### API Rate Limits

- Shopify has rate limits for API calls
- Standard plan: 2 requests per second
- Shopify Plus: 4 requests per second
- If you hit rate limits, wait a moment and try again

## Security Best Practices

1. **Keep Access Tokens Secure**
   - Never commit your `.env` file to version control
   - Use environment variables in production
   - Rotate access tokens regularly

2. **Minimal Permissions**
   - Only grant the permissions you need
   - Read access is sufficient for order verification
   - Avoid write permissions unless necessary

3. **Monitor App Usage**
   - Check your private apps regularly
   - Remove unused apps
   - Monitor API usage in Shopify admin

## Support

If you encounter issues:

1. Check the application logs for detailed error messages
2. Verify your Shopify credentials are correct
3. Test with a simple order ID first
4. Ensure your Shopify store has API access enabled

## Next Steps

Once Shopify integration is working:

1. **Test with Various Order Types**
   - Simple orders
   - Orders with custom properties
   - Orders with multiple line items

2. **Optimize Your Workflow**
   - Use Shopify integration for Shopify orders
   - Use manual input for other sources (Zendesk, Etsy, etc.)

3. **Monitor Performance**
   - Check API response times
   - Monitor error rates
   - Optimize based on usage patterns
