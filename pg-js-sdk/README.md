# Payment Gateway JS SDK
===================

## Language:: [EN](#Description) || [ES](#Descripción)


## Description
Payment Gateway JS SDK is a library that allows developers to easily connect to the Payment Gateway API for to tokenize cards with the most secure level of PCI.


## Installation 
You will need to include the SDK dependency `payment_sdk_stable.min.js` into your webpage specifying "UTF-8" like charset.

```html
<!-- PG JS SDK -->
<script src="https://cdn.paymentez.com/ccapi/sdk/payment_sdk_stable.min.js" charset="UTF-8"></script>
```


## Usage

For working examples of using Payment Gateway JS SDK, see the following [resources](https://github.com/cravgabo/pg-docs/tree/main/pg-js-sdk/examples).

### Generate tokenization reference
Our library allows to you implement the easiest way a form to tokenize cards with us. Our form is dynamic and response for each card, that's mean, you only implement this form for any card type that we support. 
You can implement following the next steps:
1. Create an element to contain the dynamic form.
2. Instance the [PaymentGateway](#PaymentGateway-class) with the required parameters.
3. Generate the tokenization reference with the required data. [generate_tokenize](#generate-tokenize-function)
4. Define the event to execute the [tokenize](#tokenize-function) action.
You only need to create a container and declare when you need that it will be inserted with a dynamic form with all required validations to capture the sensitive data.
```html
<!-- 1. Create an element to contain the dynamic form. -->
<body>
  <div id='tokenize_example'></div>
  <div id="messages"></div>
  <div class="button_container">
    <button id='tokenize_btn' class='btn'>Save card</button>
  </div>
</body>
<script>
  (function() {
    let submitButton = document.querySelector('#tokenize_btn');
    let submitInitialText = submitButton.textContent;
    // 2. Instance the [PaymentGateway](#PaymentGateway-class) with the required parameters.
    let pg_sdk = new PaymentGateway(environment, application_code, application_key);
    let tokenize_data = {
      locale: 'en',
      user: {
        id: '117',
        email: 'jhon@doe.com',
      },
      configuration: {
        default_country: 'COL',
      },
    }

    let incompleteFormCallback = message => {
      // document.getElementById('messages').innerHTML = JSON.stringify(message)
      submitButton.innerText = submitInitialText;
      submitButton.removeAttribute("disabled");
    }

    let responseCallback = response => {
      document.getElementById('messages').innerHTML = JSON.stringify(response)
      submitButton.innerText = 'Save new card';
      submitButton.removeAttribute("disabled");
      submitButton.addEventListener('click', event => {
        window.location.reload();
      });
    }
 
    // 3. Generate the tokenization reference with the required data. [generate_tokenize](#generate_tokenize-function)
    pg_sdk.generate_tokenize(tokenize_data, '#tokenize_example', responseCallback, incompleteFormCallback);

    // 4. Define the event to execute the [tokenize](#tokenize-function) action.
    submitButton.addEventListener('click', event => {
      submitButton.innerText = 'Card Processing...';
      submitButton.setAttribute("disabled", "disabled");
      pg_sdk.tokenize();
      event.preventDefault();
    });
  })();
</script>
```
 
### PaymentGateway Class
Contain all functionality to connect with the Payment Gateway and must be instanced with the following parameters: 

| Field              | Description                        | Type   | Required |
| :----------------- | :--------------------------------- |:------ | :------- |
| Environment        | Environment to operate (stg, prod) | String |     X    |
| Application Code   | Provided by the Payment Gateway    | String |     X    |
| Application Key    | Provided by the Payment Gateway    | String |     X    |


### Generate Tokenize function
Execute this function to generate the dynamic form, and insert within the specified container.
Call with the following parameters:

| Field                       | Description                                                                                          | Type     | Required |
| :-------------------------- | :--------------------------------------------------------------------------------------------------- |:-------- | :------- |
| [Tokenize data](#tokenize-data)|                                                                                                   | Object   |     X    |
| Container query selector    | Query selector by identify the html element                                                          | String   |     X    |
| Response callback           | Callback to receive the token                                                                        | function |     X    |
| Incomplete form callback    | Callback to execute when the [tokenize](#tokenize-function) is requested, but the form is incomplete | function |     X    |


### Tokenize function
Execute this function to request the tokenize, this function validate by default all data set by the client in the form. In case the data is incomplete or incorrect, the incomplete form callback will be invoked.

### Tokenize Data

```javascript
    let tokenize_data = {
      locale: 'en',
      user: {
        id: '117',
        email: 'jhon@doe.com',
      },
      configuration: {
        default_country: 'COL',
      },
    }
```

| Field                                   | Description                                                                 | Type   | Required |
| :-------------------------------------- | :-------------------------------------------------------------------------- |:------ | :------- |
| locale                                  | Language to use (pt, es, en)                                                | String |          |
| user.id                                 | Customer identifier. This is the identifier you use inside your application | String |      X   |
| user.email                              | Buyer email, with valid e-mail format                                       | String |      X   |
| configuration.default_country           | Country in format ISO 3166-1 Alpha 3                                        | String |          |
| configuration.icon_colour               | Icons color                                                                 | String |          |
| configuration.use_dropdowns             | Use dropdowns to set the card expiration date                               | String |          |
| configuration.exclusive_types           | Define allowed card types                                                   | String |          |
| configuration.invalid_card_type_message | Define a custom message to show for invalid card types                      | String |          |
