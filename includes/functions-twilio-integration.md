Cet exemple implique l’utilisation du service [Twilio](https://www.twilio.com/) pour envoyer des SMS à un téléphone mobile. Azure Functions prend déjà ce service en charge, via la [liaison Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio). L’exemple utilise cette fonctionnalité.

Pour commencer, vous devez disposer d’un compte Twilio. Vous pouvez en créer un gratuitement à l’adresse https://www.twilio.com/try-twilio. Une fois ce compte créé, ajoutez les trois **paramètres d’application** suivants à votre application de fonction.

| Nom du paramètre d’application | Description de la valeur |
| - | - |
| **TwilioAccountSid**  | Il s’agit du SID de votre compte Twilio |
| **TwilioAuthToken**   | Il s’agit du jeton d’authentification de votre compte Twilio |
| **TwilioPhoneNumber** | Numéro de téléphone associé à votre compte Twilio. Il est utilisé pour envoyer des SMS. |