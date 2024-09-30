# 🎓 Open edX Razorpay Extension

This repository contains an extension for integrating the Razorpay payment gateway into the Open edX platform. It enables institutions to process payments seamlessly through Razorpay. 💳

## 🚀 Integration Steps

Follow these steps to integrate Razorpay into Open edX:

## Steps to Integrate Razorpay into Open edX:

1. **Install the extension:**
   ```bash
   git clone https://github.com/Primzel/edx-razorpay-extension
   #cp edx-razorpay-extension /path/to/openedx/ecommerce/requirements/
   pip install edx-razorpay-extension
   ```

2. **Update the payment method** in `openedx/frontend-app-payment/src/payment/payment-methods/paypal/service.js`:
   ```bash   
   payment_processor: 'razorpay'`
   ```

3. **Add the following configuration** in `openedx/config.yml`:
   ```yaml
    ECOMMERCE_ENABLED_PAYMENT_PROCESSORS:
      - razorpay
    ECOMMERCE_EXTRA_PAYMENT_PROCESSOR_CLASSES:
      - extensions.payment.processors.razorpay.RazorPay
    ECOMMERCE_EXTRA_PAYMENT_PROCESSOR_URLS:
      razorpay: extensions.payment.processors.razorpay.urls
    ECOMMERCE_PAYMENT_PROCESSORS:
      razorpay:
        cancel_checkout_path: /checkout/cancel-checkout/
        client_id: rzp_test_hvDy9KJ3lpn9js
        client_secret: TogGMrqqeWqQxIqzC0VIeDx7
        error_url: /checkout/error/
        lms_form_url: http://local.edly.io/dashboard
        mode: sandbox
        receipt_url: /checkout/receipt/
    ```
   
      
4. **Modify `openedx/ecommerce/ecommerce/extensions/api/v2/views/checkout.py`**:
   In the `CheckoutView` class, update the `post` method:
   ```python
   request.data['payment_processor'] = 'razorpay'
   payment_processor_name = 'razorpay'
   ```

5. **Modify `ecommerce/ecommerce/settings/_oscar.py`**:
   ```python
   PAYMENT_PROCESSORS = ('extensions.payment.processors.razorpay.RazorPay',)
   PAYMENT_PROCESSOR_CONFIG = {
     'edx': {
       'razorpay': {
           'mode': 'sandbox',
           'client_id': 'rzp_test_hvDy9KJ3lpn9js',
           'client_secret': 'TogGMrqqeWqQxIqzC0VIeDx7',
           'cancel_checkout_path': PAYMENT_PROCESSOR_CANCEL_PATH,
           'error_path': PAYMENT_PROCESSOR_ERROR_PATH,
           'receipt_url': PAYMENT_PROCESSOR_RECEIPT_PATH,
       },
   ```

6. **Modify `openedx/ecommerce/ecommerce/settings/production.py`**:
   ```python
     PAYMENT_PROCESSORS = {
     'razorpay': {
       'public_key': 'rzp_test_hvDy9KJ3lpn9js',
       'secret_key': 'TogGMrqqeWqQxIqzC0VIeDx7',
      }
   }
   ```

7. **Modify `ecommerce/ecommerce/settings/local.py`**:
   ```python
     PAYMENT_PROCESSOR_CONFIG = {
     'razorpay': {
           'mode': 'sandbox',  # Use 'live' for production
           'client_id': 'rzp_test_hvDy9KJ3lpn9js',
           'client_secret': 'TogGMrqqeWqQxIqzC0VIeDx7',
           'receipt_path': PAYMENT_PROCESSOR_RECEIPT_PATH,
           'cancel_checkout_path': PAYMENT_PROCESSOR_CANCEL_PATH,
           'error_path': PAYMENT_PROCESSOR_ERROR_PATH,
           'currency': 'INR',  # Specify currency
       },
   }
   ```
8. **Modify `ecommerce/ecommerce/settings/devstack.py`**:
   ```python
     PAYMENT_PROCESSOR_CONFIG = {
     'razorpay': {
           'mode': 'sandbox',
           'client_id': 'rzp_test_hvDy9KJ3lpn9js',
           'client_secret': 'TogGMrqqeWqQxIqzC0VIeDx7',
           'cancel_checkout_path': PAYMENT_PROCESSOR_CANCEL_PATH,
           'error_path': PAYMENT_PROCESSOR_ERROR_PATH,
       },
   }
   ```
9. **Modify `env/plugins/ecommerce/apps/ecommerce/settings/production.py`**:
   ```python
   PAYMENT_PROCESSORS = list(PAYMENT_PROCESSORS) + ['extensions.payment.processors.razorpay.RazorPay']
   EXTRA_PAYMENT_PROCESSOR_URLS["razorpay"] = "extensions.payment.processors.razorpay.urls"
   ```

10. **Modify `env/plugins/ecommerce/apps/ecommerce/settings/development.py`**:
   ```python
   PAYMENT_PROCESSORS = ['extensions.payment.processors.razorpay.RazorPay']
   EXTRA_PAYMENT_PROCESSOR_URLS["razorpay"] = "extensions.payment.processors.razorpay.urls"
   ```

11. **Modify `edx-razorpay-extension/extensions/payment/processors/razorpay/processor.py`**:
   ```python
    redirect_url = payment["short_url"]  # Ensure this URL hits in the GET method.
   ```

12. **Modify `edx-razorpay-extension/extensions/payment/processors/razorpay/views.py`**:
   ```python
    receipt_url = get_receipt_page_url(
    request,
    order_number=basket.order_number,
    site_configuration=basket.site.siteconfiguration,
    disable_back_button=True,
)
   ```

13. **Modify `<Ecommerce-link>/admin)`**:
    - siteconfiguration:
        - Client-side payment processor:razorpay
        - Payment processors:razorpay
    - Switches:
        - Create:
          - payment_processor_active_razorpay
             - is_active: True
