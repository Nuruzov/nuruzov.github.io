<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, userscalable=no">
    <title>Freedom Pay</title>
    <style>
      html,
      body {
        overflow-x: hidden;
      }

      body {
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 20px;
        background-color: #f4f4f4;
      }

      form {
        display: flex;
        flex-direction: column;
        max-width: 500px;
        margin: 0 auto;
        background: white;
        padding: 20px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        border-radius: 8px;
      }

      .form-group {
        margin-bottom: 15px;
      }

      label {
        font-size: 16px;
        margin-bottom: 5px;
      }

      input {
        padding: 10px;
        font-size: 16px;
        border: 1px solid #ccc;
        border-radius: 4px;
        width: calc(100% - 22px);
      }

      button {
        padding: 10px 20px;
        font-size: 18px;
        color: white;
        background-color: #007bff;
        border: none;
        border-radius: 5px;
        transition: background-color 0.3s;
      }

      button:hover {
        background-color: #0056b3;
      }

      .error {
        color: red;
        font-size: 12px;
        margin-top: 5px;
        display: block;
      }

      .loader {
        display: inline-block;
        margin-left: 8px;
        font-size: 18px;
      }
    </style>
    <script>
      (function(f, p, s, d, k) {
        d = f.createElement(p);
        k = f.getElementsByTagName(p)[0];
        d.src = 'https://cdn.freedompay.kz/sdk/js-sdk-1.0.0.js';
        k.parentNode.insertBefore(d, k);
      })(document, 'script');
    </script>
  </head>
  <body>
    <h1 id="OrderNum">Оплата заказа № <span id="orderId"></span>
    </h1>
    <form onsubmit="validateForm()">
      <h2>Введите данные карты</h2>
      <div class="form-group">
        <label for="card_number">Номер карты:</label>
        <input type="text" id="card_number" name="card_number" value="" placeholder="1234 5678 9012 3456" required>
        <span id="card_number_error" class="error"></span>
      </div>
      <div class="form-group">
        <label for="card_holder_name">Владелец карты:</label>
        <input type="text" id="card_holder_name" name="card_holder_name" value="" placeholder="Алихан Нурлыбаев" required>
        <span id="card_holder_name_error" class="error"></span>
      </div>
      <div class="form-group">
        <label for="card_exp_month">Месяц:</label>
        <input type="text" id="card_exp_month" name="card_exp_month" value="" placeholder="01" required>
        <span id="card_exp_month_error" class="error"></span>
      </div>
      <div class="form-group">
        <label for="card_exp_year">Год:</label>
        <input type="text" id="card_exp_year" name="card_exp_year" value="" placeholder="24" required>
        <span id="card_exp_year_error" class="error"></span>
      </div>
      <div class="form-group">
        <label for="card_cvv">CVV:</label>
        <input type="password" id="card_cvv" name="card_cvv" value="" placeholder="123" required>
        <span id="card_cvv_error" class="error"></span>
      </div>
      <button type="submit" id="SubmitBtn" style="display: flex; justify-content:
center;"> Оплатить <span id="loader" class="loader" style="display: none;">Загрузка...</span>
      </button>
    </form>
    <div style="visibility: hidden; width: 100%; height: 90vh;" id="3dsForm"></div>
    <script>
      window.onload = function() {
        FreedomPaySDK.setup('-----BEGIN PUBLIC KEY-----MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAoSPUCG9c19rnWdOZOeA1mpSpJwp7JT+6GwQ4MItZ5FOZJHQyc5Tf1K+gwHsO88bL1DSqylu6mP7QNNf5fnjN/yKd387hxsMVXYljsmJABsJPmX4UbmpDDNU+M0uXsw9GA1+v7WOukRMpGw23tHPOLBw+GB0QS5k6+HdFUgdVWuSslGMZClLbGe3xHwOFPEXM2rC/ymnxvedeyfUDUwzE2IX8pByKRxDlYERgAjtr+A+Qp4epN6LvHQle63EK/8NCUvBnRtR3sgjiqMD6HsV/8czROFSLGt6u0/ZJ2jJC4jSreCf7F33Ftss7ZrA95uZucBUjedn5taCSfC+JmjbV/QIDAQAB-----END PUBLIC KEY-----', 'dnwxT3isxnA1lnoiEuruLakXQ8w8oKli');
      }
      orderNum = Math.floor(Math.random() * 90000) + 10000 + "";
      document.getElementById('orderId').textContent = orderNum;

      async function pay() {
        document.getElementById('SubmitBtn').disabled = true;
        document.getElementById('loader').style.display = 'inline-block';
        const JSPaymentOptions = {
          order_id: orderNum,
          auto_clearing: 1,
          amount: 44,
          currency: "KZT",
          description: 'description',
          test: 0,
          options: {
            custom_params: {},
            user: {
              email: 'email@email.com',
              phone: '+77771112233'
            }
          },
        };
        const JSTransactionOptionsBankCard = {
          type: 'bank_card',
          options: {
            card_number: document.getElementById('card_number').value,
            card_holder_name: document.getElementById('card_holder_name').value,
            card_exp_month: document.getElementById('card_exp_month').value,
            card_exp_year: document.getElementById('card_exp_year').value,
            card_cvv: document.getElementById('card_cvv').value
          }
        };
        try {
          let JSPayResult = await FreedomPaySDK.charge(JSPaymentOptions, JSTransactionOptionsBankCard);
          console.log(JSPayResult);
          if (JSPayResult.payment_status === "need_confirm") {
            document.querySelector('form').style.display = 'none';
            document.getElementById('OrderNum').style.display = 'none';
            document.getElementById('3dsForm').style.visibility = 'visible';
            JSPayResult = await FreedomPaySDK.confirmInIframe(JSPayResult, "3dsForm");
          }
          console.log(JSPayResult);
          if (JSPayResult.payment_status === "success") {
            console.log("victory");
            alert("Оплата прошла успешно!");
            window.location.href = "https://freedompay.kz"; // Redirect to the desired URL
          }
        } catch (JSErrorObject) {
          console.log(JSErrorObject);
          alert(JSErrorObject.response.error_message);
          document.getElementById('SubmitBtn').disabled = false;
          document.getElementById('loader').style.display = 'none';
        }
      }

      function validateForm() {
        event.preventDefault();
        // Clear previous errors
        document.getElementById('card_number_error').textContent = '';
        document.getElementById('card_holder_name_error').textContent = '';
        document.getElementById('card_exp_month_error').textContent = '';
        document.getElementById('card_exp_year_error').textContent = '';
        document.getElementById('card_cvv_error').textContent = '';
        let isValid = true;
        // Validate card number using Luhn algorithm
        const cardNumber = document.getElementById('card_number').value;
        if (!validateCardNumber(cardNumber)) {
          document.getElementById('card_number_error').textContent = 'Неправильный номер карты';
          isValid = false;
        }
        // Validate card holder name
        const cardHolderName = document.getElementById('card_holder_name').value;
        if (!/^[a-zA-ZА-Яа-я\s]+$/.test(cardHolderName)) {
          document.getElementById('card_holder_name_error').textContent = 'Неправильное имя';
          isValid = false;
        }
        // Validate expiration month and year
        const cardExpMonth = document.getElementById('card_exp_month').value;
        const cardExpYear = document.getElementById('card_exp_year').value;
        if (!/^\d{2}$/.test(cardExpMonth) || parseInt(cardExpMonth) < 1 || parseInt(cardExpMonth) > 12) {
          document.getElementById('card_exp_month_error').textContent = 'Неправильный месяц';
          isValid = false;
        }
        if (!/^\d{2}$/.test(cardExpYear) || parseInt(cardExpYear) < 0 || parseInt(cardExpYear) > 99) {
          document.getElementById('card_exp_year_error').textContent = 'Неправильный год';
          isValid = false;
        }
        // Validate CVV
        const cardCvv = document.getElementById('card_cvv').value;
        if (!/^\d{3,4}$/.test(cardCvv)) {
          document.getElementById('card_cvv_error').textContent = 'Неправильный CVV';
          isValid = false;
        }
        if (isValid) {
          pay();
        }
      }

      function validateCardNumber(cardNumber) {
        const sanitizedCardNumber = cardNumber.replace(/\D/g, '');
        let sum = 0;
        let shouldDouble = false;
        for (let i = sanitizedCardNumber.length - 1; i >= 0; i--) {
          let digit = parseInt(sanitizedCardNumber.charAt(i));
          if (shouldDouble) {
            digit *= 2;
            if (digit > 9) {
              digit -= 9;
            }
          }
          sum += digit;
          shouldDouble = !shouldDouble;
        }
        return sum % 10 === 0;
      }
    </script>
  </body>
</html>
