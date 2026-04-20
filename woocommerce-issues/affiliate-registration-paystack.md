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

## Code Snippets

### 1. Backend Registration & Commission Logic
add_action('wp_ajax_basic_referral_handler', 'handle_basic_referral_registration');
add_action('wp_ajax_nopriv_basic_referral_handler', 'handle_basic_referral_registration');

function handle_basic_referral_registration() {
    $data = json_decode(file_get_contents('php://input'), true);

    if (empty($data['email']) || empty($data['affiliate_id'])) {
        wp_send_json(['success' => false, 'message' => 'Missing required data.']);
    }

    $email = sanitize_email($data['email']);
    $full_name = sanitize_text_field($data['full_name']);
    $affiliate_id = intval($data['affiliate_id']);
    $reference = sanitize_text_field($data['reference']);

    // Create user
    if (email_exists($email)) {
        wp_send_json(['success' => false, 'message' => 'Email already registered.']);
    }

    $user_id = wp_create_user($email, $data['password'], $email);

    // Assign membership
    if (function_exists('wc_memberships_create_user_membership')) {
        wc_memberships_create_user_membership([
            'plan_id' => 'basic-membership',
            'user_id' => $user_id,
            'status'  => 'active'
        ]);
    }

    // Log affiliate commission
    if (function_exists('affiliate_wp')) {
        affiliate_wp()->referrals->add([
            'affiliate_id' => $affiliate_id,
            'amount'       => 6250,
            'reference'    => $reference,
            'context'      => 'membership_referral',
            'status'       => 'unpaid'
        ]);
    }

    wp_send_json(['success' => true]);
}

### 2. Paystack Payment Integration
const handler = PaystackPop.setup({
    key: 'pk_live_xxxxxxxxxxxxxxxxx', // mask this
    email: email,
    amount: 3000000,
    currency: 'NGN',
    metadata: {
        referral_id: referral_id
    },
    callback: function (response) {
        const reference = response.reference;

        if (referral_id) {
            window.location.href = `/affiliate-success/?reference=${reference}&ref=${referral_id}`;
        } else {
            window.location.href = `/direct-success/?reference=${reference}`;
        }
    }
});

### 3. Registration Form (Simplified)
<form id="basic-membership-referral-form">
  <input type="text" name="full_name" required />
  <input type="email" name="email" required />
  <input type="tel" name="phone" required />
  <input type="text" name="affiliate_id" required />
  <input type="password" name="password" required />
  <button type="submit">Join Basic Plan</button>
</form>
