# 🎓 Open edX Razorpay Extension

This repository contains an extension for integrating the Razorpay payment gateway into the Open edX platform. It enables institutions to process payments seamlessly through Razorpay. 💳

## 🚀 Integration Steps

Follow these steps to integrate Razorpay into Open edX:

## Steps to Integrate Razorpay into Open edX:

1. **Install the extension:**
   ```bash
   git clone https://github.com/Primzel/edx-razorpay-extension
   cp edx-razorpay-extension /path/to/openedx/ecommerce/requirements/
   pip install edx-razorpay-extension

   
- 1 Go to openedx/frontend-app-payment/src/payment/payment-methods/paypal/service.js and change payment_processor
   'paypal' to payment_processor: 'razorpay'
  
- 2 Now add following things in openedx/config.yml
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
      
- 3 Now do the following changes in openedx/ecommerce/ecommerce/extensions/api/v2/views/checkout.py
    In class CheckoutView > def post 
      request.data['payment_processor']='razorpay'
      payment_processor_name = 'razorpay'

- 4 In ecommerce/ecommerce/settings/_oscar.py
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

- 5 In openedx/ecommerce/ecommerce/settings/production.py
    PAYMENT_PROCESSORS = {
      'razorpay': {
        'public_key': 'rzp_test_hvDy9KJ3lpn9js',
        'secret_key': 'TogGMrqqeWqQxIqzC0VIeDx7',
       }
    }

- 6 In ecommerce/ecommerce/settings/local.py
    PAYMENT_PROCESSOR_CONFIG = {
      'razorpay': {
            'mode': 'sandbox',  # Use 'live' for production
            'client_id': 'rzp_test_hvDy9KJ3lpn9js',  # Your Razorpay Client ID
            'client_secret': 'TogGMrqqeWqQxIqzC0VIeDx7',  # Your Razorpay Client Secret
            'receipt_path': PAYMENT_PROCESSOR_RECEIPT_PATH,
            'cancel_checkout_path': PAYMENT_PROCESSOR_CANCEL_PATH,
            'error_path': PAYMENT_PROCESSOR_ERROR_PATH,
            'currency': 'INR',  # Specify currency
        },
    }

- 7 In ecommerce/ecommerce/settings/devstack.py
    PAYMENT_PROCESSOR_CONFIG = {
      'razorpay': {
            'mode': 'sandbox',
            'client_id': 'rzp_test_hvDy9KJ3lpn9js',
            'client_secret': 'TogGMrqqeWqQxIqzC0VIeDx7',
            'cancel_checkout_path': PAYMENT_PROCESSOR_CANCEL_PATH,
            'error_path': PAYMENT_PROCESSOR_ERROR_PATH,
        },
    }

- 8 In env/plugins/ecommerce/apps/ecommerce/settings/production.py
    PAYMENT_PROCESSORS = list(PAYMENT_PROCESSORS) + ['extensions.payment.processors.razorpay.RazorPay']
    EXTRA_PAYMENT_PROCESSOR_URLS["razorpay"] = "extensions.payment.processors.razorpay.urls"

- 9 In env/plugins/ecommerce/apps/ecommerce/settings/development.py
    PAYMENT_PROCESSORS = ['extensions.payment.processors.razorpay.RazorPay']
    EXTRA_PAYMENT_PROCESSOR_URLS["razorpay"] = "extensions.payment.processors.razorpay.urls"

- 10 In edx-razorpay-extension/extensions/payment/processors/razorpay/processor.py
     def get_transaction_parameters >
       redirect_url=payment["short_url"] 
     make above URL hit in get method. #change method 

- 11 In edx-razorpay-extension/extensions/payment/processors/razorpay/views.py
     def get > 
       receipt_url = get_receipt_page_url(
            request,
            order_number=basket.order_number,
            site_configuration=basket.site.siteconfiguration,
            disable_back_button=True,
        )
