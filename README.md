docmail-laravel
===============

## Installation

The Docmail Service Provider can be installed via [Composer](http://getcomposer.org) by requiring the `gosoftware/docmail` package and setting the `minimum-stability` to `dev` in your project's `composer.json`.

```json
{
    "require": {
        "laravel/framework": "5.*",
        "gosoftware/docmail": "1.*"
    },
    "minimum-stability": "dev"
}
```

Run the command `php artisan vendor:publish` which will generate config and view files for you. Modify generated `config/docmail.php` file to your needs. If necessary, modify generated view file and `resources/views/vendor/docmail`.


This package contains two classes:

- Docmail
- DocmaiAPI

DocmailAPI class
----------------

This class allows making Docmail API calls. Every public function in DocmailAPI class is mapped to a single Docmail API call.

    $templateGUID = DocmailAPI::AddTemplateFile($options);

This code send a AddTemplateFile call to the API with parameters $option and results the template GUID.

Docmail class
-------------
Docmail class contains complex methods, not only single API calls. For example the following method Docmail::sendToSingelAddress creates a new mailing, adds an address and uploads a template file:

    public function sendToSingelAddress($options = []) {

        $this->mailingGUID = DocmailAPI::CreateMailing();
        $options["MailingGUID"] = $this->mailingGUID;

        $result = DocmailAPI::AddAddress($options);

        $this->templateGUID = DocmailAPI::AddTemplateFile($options);

    }

In your code you can combine the two classes, like:

            $dm = new Docmail();
            $dm->sendToSingelAddress([
                "Address1" => "address line 1",
                "FilePath" => "../sample.pdf",
            ]);
            $satus = DocmailAPI::GetStatus($dm->getMailingGUID());

API parameter defaults
----------------------

API call parameters can get its values from various sources (in ascending priority order):

- method parameters

        DocmailAPI::GetStatus($dm->getMailingGUID())

- docmail config file (/app/config/Softlabs/docmail.php)

        return array(

            'username'       => 'myusername',
            'password'       => 'mypassword',
            'wsdl'           => 'https://www.cfhdocmail.com/TestAPI2/DMWS.asmx?WSDL',

            'productType'    => "A4Letter",
            'printColor'     => false,
            'printDuplex'    => false,
            'deliveryType'   => "Standard",
            'despatchASAP'   => true,

            'MinimumBalance' => 200,
            'AlertEmail'     => "istvan.kadar@softlabs.co.uk"

        );

- defalut values set in DocmailAPI class

        private static $defaults = [
            "Username" => null,
            "Password" => null,
            "wsdl" => null,
            "timeout" => 240,
            "DocumentType" => "A4Letter"
        ];

Example code how to send a mailing
---------------------------------

        $data = [
            "lastName"     => "lastname2",
            "address1"     => "address line 1",
            "postCode"     => "PostCode",
            "filePath"     => "../sample.pdf",
            "templateName" => "Sample Template 01",
            "submit"       => true,
        ];

        $options = [
            "printColour"  => true,
            "firstClass"   => true,
        ];

        $result = \GoSoftware\Docmail\Docmail::sendToSingelAddress($data, $options);

