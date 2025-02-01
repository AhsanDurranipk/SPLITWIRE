<?php
/* Template Name: Checkout Page */

get_header();  // Include the header

?>

<!-- Payment Form HTML -->
<script src="https://secure.nmi.com/token/Collect.js"></script>

<h2>Secure Payment - Checkout</h2>
<p>Total Amount: <strong>$300</strong></p>

<form id="payment-form">
    <label>Full Name:</label>
    <input type="text" id="name" required>

    <label>Card Number:</label>
    <input type="text" id="cc-number" data-tokenize="true" data-tokenization-key="checkout_public_5S8a52DwTa2655H966KdxMeKbJQ7sT6c" required>

    <label>Expiry Month:</label>
    <input type="text" id="cc-exp-month" required>

    <label>Expiry Year:</label>
    <input type="text" id="cc-exp-year" required>

    <label>CVV:</label>
    <input type="text" id="cc-cvv" required>

    <button type="submit">Pay $300</button>
</form>

<p id="payment-status"></p>

<script>
document.getElementById('payment-form').addEventListener('submit', function(event) {
    event.preventDefault();

    CollectJS.tokenize({
        publicKey: "your-public-key",  //
        tokenizationKey: "checkout_public_5S8a52DwTa2655H966KdxMeKbJQ7sT6c",  // 
        card: {
            number: document.getElementById('cc-number').value,
            expirationMonth: document.getElementById('cc-exp-month').value,
            expirationYear: document.getElementById('cc-exp-year').value,
            cvv: document.getElementById('cc-cvv').value
        }
    }, function(response) {
        if (response.status === 200) {
            processPayment(response.token);
        } else {
            document.getElementById("payment-status").innerText = "Payment Failed: " + response.message;
        }
    });
});

function processPayment(token) {
    fetch('<?php echo site_url(); ?>/wp-json/payment/process', {  
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            token: token,
            amount: "300.00"
        })
    })
    .then(response => response.json())
    .then(data => {
        if (data.success) {
            document.getElementById("payment-status").innerText = "Payment Successful!";
        } else {
            document.getElementById("payment-status").innerText = "Payment Failed!";
        }
    })
    .catch(error => console.error('Error:', error));
}
</script>

<?php
get_footer();  // Include the footer
?>
