# Affiliate-Based Paid Membership Registration with Paystack Integration

## Overview
Implemented a custom registration and payment workflow for a WordPress e-commerce platform with an affiliate/referral system. The solution supported paid memberships and automated commission tracking for referring affiliates.

## Problem
The platform required:
- Paid user registration (Basic & Premium membership tiers)
- Seamless payment processing
- Automatic affiliate commission tracking for referrals
- A smooth user experience without redirect issues

Default tools did not fully support this combined workflow out-of-the-box.

## Solution
Developed a custom registration and payment flow integrated with Paystack and AffiliateWP.

### Key Implementations:
- Built custom registration forms for Basic and Premium memberships  
- Integrated Paystack payment gateway using a custom handler  
- Connected successful payments to membership activation (WooCommerce Memberships)  
- Captured referral IDs during registration  
- Logged affiliate commissions automatically upon successful payment  
- Ensured correct parent affiliate attribution for referred users  

## Workflow
1. User selects membership plan (Basic or Premium)  
2. User fills registration form  
3. Payment is triggered via Paystack  
4. On successful payment:
   - User account is created  
   - Membership is assigned  
   - Referral ID is processed  
   - Commission is logged for the referring affiliate  

## Code Snippet (Example)

```php
// Simplified Paystack callback handler
add_action('init', function() {
    if (isset($_GET['payment_reference'])) {
        $reference = sanitize_text_field($_GET['payment_reference']);

        // Verify payment (pseudo logic)
        $payment_verified = verify_paystack_payment($reference);

        if ($payment_verified) {
            // Create user & assign membership
            // Capture referral ID
            // Log affiliate commission
        }
    }
});
