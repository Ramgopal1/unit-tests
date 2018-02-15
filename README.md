TEST SPEC
______________________________________________________________________

<!doctype html>
<html>

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <meta name="description" content="">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="apple-mobile-web-app-capable" content="yes">

    <script src="../../polymer/webcomponentsjs/webcomponents.min.js"></script>
    <script src="../../polymer/web-component-tester/browser.js"></script>
    <script src="../../polymer/test-fixture/test-fixture-mocha.js"></script>

    <link rel="import" href="../../polymer/polymer/polymer.html">
    <link rel="import" href="../../polymer/test-fixture/test-fixture.html">
    <link rel="import" href="../../ing-globals/mock-globals.html">
    <link rel="import" href="../ing-crs-tax-residence-country.html">
</head>

<body>
    <test-fixture id="TestFixture">
        <template>
            <ing-crs-tax-residence-country></ing-crs-tax-residence-country>
        </template>
    </test-fixture>

    <script>
        var should = chai.should();
        describe('ing-crs-tax-residence-country', function() {
            var $el;
            var initialData = {
                "Countries": [{
                    "Code": "A",
                    "Name": "A",
                    "TinAvailability": null
                }, {
                    "Code": "B",
                    "Name": "B",
                    "TinAvailability": null
                }],
                "TaxResidencyReasons": [{
                    "Code": "",
                    "Description": "Yes - I can provide TIN or equivalent"
                }, {
                    "Code": "A",
                    "Description": "No - the country/jurisdiction does not issue TINs to its residents"
                }, {
                    "Code": "B",
                    "Description": "No - I am unable to obtain a TIN or equivalent"
                }, {
                    "Code": "C",
                    "Description": "No - a TIN or equilvanet is not required (only select this reason if the domestic law of the relevant jurisdiction does not require the collection of the TIN issued by such jurisdiction)"
                }]
            };

            before(function(done) {
                $el = fixture('TestFixture');
                flush(done);
            });

            it('should have a shady dom', function() {
                expect($el.shadyRoot).to.exist;
            });

            it('should be in default state', function() {
                expect($el.initialData).to.not.exist;
                expect($el.data).to.not.exist;
                expect($el.countries).to.exist;
                expect($el.countries.length).to.equal(0);
                expect($el.reasons).to.exist;
                expect($el.reasons.length).to.equal(0);
                expect($el.selectedCountryCode).to.not.exist;
                expect($el.selectedCountryIndex).to.equal(-1);
                expect($el.selectedReasonCode).to.not.exist;
                expect($el.selectedReasonIndex).to.equal(-1);
                expect($el.tin).to.equal('');
                expect($el.explanation).to.equal('');
                expect($el.showTinOrEquivalentInput).to.be.false;
                expect($el.showExplanationInput).to.be.false;
                expect($el.validate()).to.be.false;
            });

            describe('set initialData property', function() {
                var spy;

                before(function(done) {
                    spy = sinon.spy($el, '_setInitialData');
                    $el.reset();
                    $el.initialData = initialData;
                    flush(done);
                });

                it('_setInitialData should be called once', function() {
                    expect(spy.calledOnce).to.be.true;
                });

                it('_setInitialData should be called with the correct data', function() {
                    expect(spy.calledWith(initialData)).to.be.true;
                });

                it('initialData should exist', function() {
                    expect($el.initialData).to.exist;
                });

                it('countries should exist', function() {
                    expect($el.countries).to.exist;
                });

                it('countries should match length', function() {
                    expect($el.countries.length).to.be.equal(initialData.Countries.length);
                });

                it('reasons should exist', function() {
                    expect($el.reasons).to.exist;
                });

                it('reasons should match length', function() {
                    expect($el.reasons.length).to.be.equal(initialData.TaxResidencyReasons.length);
                });
            });

            describe('data property', function() {
                before(function(done) {
                    $el = fixture('TestFixture');
                    $el.initialData = initialData;
                    flush(done);
                });

                describe('set default data', function() {
                    var expected = {}
                    var spy;

                    before(function(done) {
                        spy = sinon.spy($el, '_setData');
                        $el.data = expected;
                        flush(done);
                    });

                    it('_setData should be call once', function() {
                        expect(spy.calledOnce).to.be.true;
                    });

                    it('_setData should be called with the correct data', function() {
                        expect(spy.calledWith(initialData, expected)).to.be.true;
                    });

                    it('data should exist', function() {
                        expect($el.data).to.exist;
                    });
                });

                describe('set valid data', function() {
                    var expected = {
                        "Country": "A",
                        "Reason": "",
                        "TIN": "1234",
                        "Explanation": "Sample"
                    };

                    before(function(done) {
                        $el.data = expected;
                        flush(done);
                    });

                    it('selectedCountryCode should match', function() {
                        expect($el.selectedCountryCode).to.equal(expected.Country);
                    });

                    it('reason should match', function() {
                        expect($el.selectedReasonCode).to.equal(expected.Reason);
                    });

                    it('tin should match', function() {
                        expect($el.tin).to.equal(expected.TIN);
                    });

                    it('explanation should match', function() {
                        expect($el.explanation).to.equal(expected.Explanation);
                    });

                    it('validate should return true', function() {
                        expect($el.validate()).to.be.true;
                    });
                });

                describe('set invalid data', function() {
                    before(function(done) {
                        $el.data = {};
                        flush(done);
                    });

                    it('validate should return false', function() {
                        expect($el.validate()).to.be.false;
                    });
                });

                describe('set reason requiring tin', function() {
                    before(function(done) {
                        $el.data = {
                            "Reason": ""
                        };
                        flush(done);
                    });

                    it('tin input should be visible', function() {
                        expect($el.showTinOrEquivalentInput).to.be.true;
                    });

                    it('explanation input should be hidden', function() {
                        expect($el.showExplanationInput).to.to.be.false;
                    });

                    it('validate should return false', function() {
                        expect($el.validate()).to.be.false;
                    });

                    describe('set valid tin', function() {
                        var expected = {
                            "Country": "A",
                            "Reason": "",
                            "TIN": "1234"
                        };

                        before(function(done) {
                            $el.data = expected;
                            flush(done);
                        });

                        it('tin should be set', function() {
                            expect($el.tin).to.be.equal(expected.TIN);
                        });

                        it('validate should return true', function() {
                            expect($el.validate()).to.be.true;
                        });
                    });
                });

                describe('set reason requiring explanation', function() {
                    before(function(done) {
                        $el.data = {
                            "Reason": "B"
                        };
                        flush(done);
                    });

                    it('tin input should be hidden', function() {
                        expect($el.showTinOrEquivalentInput).to.be.false;
                    });

                    it('explanation input should be visible', function() {
                        expect($el.showExplanationInput).to.to.be.false;
                    });

                    it('validate should return false', function() {
                        expect($el.validate()).to.be.false;
                    });

                    describe('set valid explanation', function() {
                        var expected = {
                            "Country": "A",
                            "Reason": "B",
                            "Explanation": "Smample"
                        };

                        before(function(done) {
                            $el.data = expected;
                            flush(done);
                        });

                        it('explanation should be set', function() {
                            expect($el.explanation).to.be.equal(expected.Explanation);
                        });

                        it('validate should return true', function() {
                            expect($el.validate()).to.be.false;
                        });
                    });
                });
            });

            describe('selectedCountryIndex property', function() {
                before(function(done) {
                    $el.reset();
                    $el.initialData = initialData;
                    flush(done);
                });

                describe('set to 0', function() {
                    var spy;

                    before(function(done) {
                        spy = sinon.spy($el, '_selectedCountryIndexChaged');
                        $el.selectedCountryIndex = 0;
                        flush(done);
                    });

                    it('_selectedCountryIndexChaged should be called once', function() {
                        expect(spy.calledOnce).to.be.true;
                    });

                    it('_selectedCountryIndexChaged should be called with the correct data', function() {
                        expect(spy.calledWith(initialData.Countries, 0)).to.be.true;
                    });
                });

                describe('set to -1', function() {
                    var index = -1;
                    var expected = 'COUNTRY_CODE';
                    before(function(done) {
                        $el.selectedCountryCode = expected
                        $el.selectedCountryIndex = index;
                        flush(done);
                    });

                    it('selectedCountryCode should not change', function() {
                        expect($el.selectedCountryCode).to.equal(expected);
                    });
                });

                describe('set to 1', function() {
                    var index = 1;
                    var expected = initialData.Countries[index].Code;

                    before(function(done) {
                        $el.selectedCountryIndex = index;
                        flush(done);
                    });

                    it('selectedCountryCode should match', function() {
                        expect($el.selectedCountryCode).to.equal(expected);
                    });
                });
            });

            describe('selectedReasonIndex property', function() {
                before(function(done) {
                    $el.reset();
                    $el.initialData = initialData;
                    flush(done);
                });

                describe('set to 0', function() {
                    var spy;

                    before(function(done) {
                        spy = sinon.spy($el, '_selectedReasonIndexChanged');
                        $el.selectedReasonIndex = 0;
                        flush(done);
                    });

                    it('_selectedReasonIndexChanged should be called once', function() {
                        expect(spy.calledOnce).to.be.true;
                    });

                    it('_selectedReasonIndexChanged should be called with the correct data', function() {
                        expect(spy.calledWith(initialData.TaxResidencyReasons, 0)).to.be.true;
                    });
                });

                describe('set to -1', function() {
                    var index = -1;
                    var expected = 'REASON_CODE';

                    before(function(done) {
                        $el.selectedReasonCode = expected;
                        $el.selectedReasonIndex = index;
                        flush(done);
                    });

                    it('selectedReasonCode should not change', function() {
                        expect($el.selectedReasonCode).to.equal(expected);
                    });
                });

                describe('set to 1', function() {
                    var index = 1;
                    var expected = initialData.TaxResidencyReasons[index].Code;

                    before(function(done) {
                        $el.selectedReasonIndex = index;
                        flush(done);
                    });

                    it('selectedReasonCode should be expect', function() {
                        expect($el.selectedReasonCode).to.equal(expected);
                    });
                });

                describe('Reason change', function() {

                    var tin = '123456789';

                    var explanasion = 'some explanation';

                    var setTinAndChangeReason = function(reason1, reason2) {
                        $el.selectedReasonIndex = reason1;
                        $el.tin = tin;
                        $el.selectedReasonIndex = reason2;
                        $el.selectedReasonIndex = reason1;
                    }

                    var setExplanasionAndChangeReason = function(reason1, reason2) {
                        $el.selectedReasonIndex = reason1;
                        $el.explanasion = explanasion;
                        $el.selectedReasonIndex = reason2;
                        $el.selectedReasonIndex = reason1;
                    }

                    describe('clear tin', function() {
                        describe('change reason 0 to 1', function() {

                            before(function(done) {
                                setTinAndChangeReason(0, 1);
                                flush(done);
                            });

                            it('tin should be cleared', function() {
                                expect($el.tin).to.be.undefined;
                            });
                        });

                        describe('change reason 0 to 2', function() {

                            before(function(done) {
                                setTinAndChangeReason(0, 2);
                                flush(done);
                            });

                            it('tin should be cleared', function() {
                                expect($el.tin).to.be.undefined;
                            });
                        });

                        describe('change reason 0 to 3', function() {

                            before(function(done) {
                                setTinAndChangeReason(0, 2);
                                flush(done);
                            });

                            it('tin should be cleared', function() {
                                expect($el.tin).to.be.undefined;
                            });
                        });
                    });
                });

            });


            describe('validate Tin for US', function() {
                before(function(done) {
                    $el.reset();
                    $el.initialData = initialData;
                    flush(done);
                });

                describe('country code set to 0', function() {
                    var spy,$ele;

                    before(function(done) {
                        spy = sinon.spy($ele, '_validateUSTin');
                        selectedCountryIndex = 234;
                        flush(done);
                    });

                });

                describe('Enter the tin value', function() {
                    var index = -1;
                    var expected = 'Input_Tinvalue';
                    before(function(done) {
                        $el.inputTinvalue = expected
                        $el.selectedCountryIndex = index;
                        flush(done);
                    });

                    it('Tin should not change', function() {
                        expect($el.inputTinvalue).to.equal(expected);
                    });
                });

                describe('set to 9 digit numeric', function() {
                    var index = 234;
                    var expected = '123456789';

                    before(function(done) {
                        selectedCountryIndex = 234;
                        $el.inputTinvalue = expected;
                        flush(done);
                    });

                    it('tin value should match the length of 9digits', function() {
                        expect($el.inputTinvalue).to.equal(expected);
                    });
                });
            });

            describe('tin validation', function(){
                var validateTin = function(input){
                    before(function(done) {
                        $el.reset();
                        $el.initialData = initialData;
                        $el.data = {TIN: input.tin};
                        flush(done);
                    });

                    it(input.message, function(){
                        $el.$.tinOrEquivalentInput.value.should.equal(input.tin);
                        if(input.isValid){
                            $el.$.tinOrEquivalentInput.validate().should.to.be.true;
                        }else{
                            $el.$.tinOrEquivalentInput.validate().should.to.be.false;
                        }
                    });
                };

                describe('fail', function(){
                    describe('when tin is a number of white spaces', function(){
                        validateTin(
                          {
                              tin: '   ',
                              message: 'then the validation should fail',
                              isValid: false
                          });
                    });

                    describe('when tin is a single character', function(){
                        validateTin(
                          {
                              tin: 'a',
                              message: 'then the validation should fail',
                              isValid: false
                          });
                    });

                    describe('when tin is a string of two characters', function(){
                        validateTin(
                          {
                              tin: 'ab',
                              message: 'then the validation should fail',
                              isValid: false
                          });
                    });

                    describe('when tin is a number of white spaces with two character in between', function(){
                        validateTin(
                          {
                              tin: ' ab ',
                              message: 'then the validation should fail',
                              isValid: false
                          });
                    });

                    describe('when tin is a number of white spaces with a character in between', function(){
                        validateTin(
                          {
                              tin: '     a      ',
                              message: 'then the validation should fail',
                              isValid: false
                          });
                    });

                });

                describe('pass', function(){

                    describe('when tin is a word of three characters', function(){
                        validateTin(
                          {
                              tin: 'abc',
                              message: 'then the validation should pass',
                              isValid: true
                          });
                    });

                    describe('when tin is two character with a space in between', function(){
                        validateTin(
                          {
                              tin: 'a b',
                              message: 'then the validation should pass',
                              isValid: true
                          });
                    });

                    describe('when tin is a three-characters string in between of white spaces', function(){
                        validateTin(
                          {
                              tin: ' abc ',
                              message: 'then the validation should pass',
                              isValid: true
                          });
                    });

                    describe('when tin is a sentence', function(){
                        validateTin(
                          {
                              tin: 'abc efg hijk',
                              message: 'then the validation should pass',
                              isValid: true
                          });
                    });

                    describe('when tin is a sentence surrounded by white spaces', function(){
                        validateTin(
                          {
                              tin: ' abc efg hijk ',
                              message: 'then the validation should pass',
                              isValid: true
                        });
                    });
                });

            });

            describe('reason validation based on tin availability for the country', function() {
                var data = {
                    "Countries": [{
                            "Code": "A",
                            "Name": "A",
                            "TinAvailability": "Y"
                        },
                        {
                            "Code": "B",
                            "Name": "B",
                            "TinAvailability": "N"
                        },
                        {
                            "Code": "C",
                            "Name": "C",
                            "TinAvailability": null
                        }
                    ],
                    "TaxResidencyReasons": [{
                            "Code": "",
                            "Description": "Yes - I can provide TIN or equivalent"
                        },
                        {
                            "Code": "A",
                            "Description": "No - the country/jurisdiction does not issue TINs to its residents"
                        },
                        {
                            "Code": "B",
                            "Description": "No - I am unable to obtain a TIN or equivalent"
                        },
                        {
                            "Code": "C",
                            "Description": "No - a TIN or equilvanet is not required (only select this reason if the domestic law of the relevant jurisdiction does not require the collection of the TIN issued by such jurisdiction)"
                        }
                    ]
                };

                describe('simple situation: reason and country are fixed', function(){
                    describe('given selected country provides tin', function() {
                        before(function(done) {
                            $el = fixture('TestFixture');
                            $el.initialData = data;
                            $el.selectedCountryIndex = 0;
                            flush(done);
                        });

                        describe('when reason A is selected', function(){
                            before(function(done){
                                $el.selectedReasonIndex = 1;
                                flush(done);
                            });

                            it("reason drop down validation should fail", function() {
                                var validate = $el.$.reasonDropdown.validate();
                                validate.should.be.false;
                            });
                        });
                    });

                    describe('given selected country does not provide tin', function() {
                        before(function(done) {
                            $el.reset();
                            $el.initialData = data;
                            $el.selectedCountryIndex = 1;
                            flush(done);
                        });

                        describe('when empty reason is selected', function(){
                            before(function(done){
                                $el.selectedReasonIndex = 0;
                                flush(done);
                            });

                            it("then reason drop down validation should fail", function() {
                                var validate = $el.$.reasonDropdown.validate();
                                validate.should.be.false;
                            });
                        });
                    });

                    describe('given the tin availability is unclear for the selected country', function() {
                        before(function(done) {
                            $el.reset();
                            $el.initialData = data;
                            $el.selectedCountryIndex = 2;
                            flush(done);
                        });

                        describe('when empty reason is selected', function(){
                            before(function(done){
                                $el.selectedReasonIndex = 0;
                                flush(done);
                            });

                            it("then reason drop down validation should pass", function() {
                                var validate = $el.$.reasonDropdown.validate();
                                validate.should.be.true;
                            });
                        });

                        describe('when reason A is selected', function(){
                            before(function(done){
                                $el.selectedReasonIndex = 1;
                                flush(done);
                            });

                            it("then reason drop down validation should pass", function() {
                                var validate = $el.$.reasonDropdown.validate();
                                validate.should.be.true;
                            });
                        });
                    });
                });

                describe('complex situations', function(){
                    describe('fixed country, changing reason', function(){
                        var fixedCountryValidationTest = function(input){
                            describe(input.testId + ' - and reason ' + (input.firstReason !== '' ? input.firstReason : 'empty') + ' is selected', function() {
                                before(function(done) {
                                    $el = fixture('TestFixture');
                                    $el.initialData = data;
                                    if(input.isCountryProvidesTin === true) {
                                        $el.selectedCountryIndex = 0;
                                    } else if(input.isCountryProvidesTin === false) {
                                        $el.selectedCountryIndex = 1;
                                    } else {
                                        $el.selectedCountryIndex = 2;
                                    }
                                    flush(function(){
                                        if(input.firstReason === '') {
                                            $el.selectedReasonIndex = 0;
                                        } else if(input.firstReason === 'A'){
                                            $el.selectedReasonIndex = 1;
                                        }
                                        else if(input.firstReason === 'B'){
                                            $el.selectedReasonIndex = 2;
                                        }
                                        else {
                                            $el.selectedReasonIndex = 3;
                                        }
                                        flush(done);
                                    });
                                });

                                describe('when the reason is changed to reason ' + (input.secondReason !== '' ? input.secondReason : 'empty') , function(){
                                    before(function(done){
                                        if(input.secondReason === '') {
                                            $el.selectedReasonIndex = 0;
                                        } else if(input.secondReason === 'A'){
                                            $el.selectedReasonIndex = 1;
                                        }
                                        else if(input.secondReason === 'B'){
                                            $el.selectedReasonIndex = 2;
                                        }
                                        else {
                                            $el.selectedReasonIndex = 3;
                                        }
                                        flush(done);
                                    });

                                    if(input.validationResult === true){
                                        it('then reason drop down validation should pass', function() {
                                            var validate = $el.$.reasonDropdown.validate();
                                            validate.should.be.true;
                                        });
                                    } else {
                                        it('then reason drop down validation should fail', function() {
                                            var validate = $el.$.reasonDropdown.validate();
                                            validate.should.be.false;
                                        });
                                    }

                                    if(input.tinVisible){
                                        it('and tin input should be visible', function() {
                                            expect($el.showTinOrEquivalentInput).to.be.true;
                                        });
                                    } else {
                                        it('and tin input should not be visible', function() {
                                            expect($el.showTinOrEquivalentInput).to.be.false;
                                        });
                                    }
                                });
                            });
                        }

                        describe('given selected country provides tin', function() {
                            fixedCountryValidationTest({
                                testId: 1,
                                isCountryProvidesTin: true,
                                firstReason: '',
                                secondReason: 'A',
                                validationResult: false,
                                tinVisible: false
                            });

                            fixedCountryValidationTest({
                                testId: 2,
                                isCountryProvidesTin: true,
                                firstReason: '',
                                secondReason: 'B',
                                validationResult: true,
                                tinVisible: false
                            });


                            fixedCountryValidationTest({
                                testId: 3,
                                isCountryProvidesTin: true,
                                firstReason: 'A',
                                secondReason: '',
                                validationResult: true,
                                tinVisible: true
                            });

                            fixedCountryValidationTest({
                                testId: 4,
                                isCountryProvidesTin: true,
                                firstReason: 'A',
                                secondReason: 'B',
                                validationResult: true,
                                tinVisible: false
                            });

                            fixedCountryValidationTest({
                                testId: 5,
                                isCountryProvidesTin: true,
                                firstReason: 'B',
                                secondReason: '',
                                validationResult: true,
                                tinVisible: true
                            });

                            fixedCountryValidationTest({
                                testId: 6,
                                isCountryProvidesTin: true,
                                firstReason: 'B',
                                secondReason: 'A',
                                validationResult: false,
                                tinVisible: false
                            });
                        });

                        describe('given selected country does not provide tin', function() {
                            fixedCountryValidationTest({
                                testId: 7,
                                isCountryProvidesTin: false,
                                firstReason: '',
                                secondReason: 'A',
                                validationResult: true,
                                tinVisible: false
                            });
                            fixedCountryValidationTest({
                                testId: 8,
                                isCountryProvidesTin: false,
                                firstReason: '',
                                secondReason: 'B',
                                validationResult: true,
                                tinVisible: false
                            });
                            fixedCountryValidationTest({
                                testId: 9,
                                isCountryProvidesTin: false,
                                firstReason: 'A',
                                secondReason: '',
                                validationResult: false,
                                tinVisible: false
                            });
                            fixedCountryValidationTest({
                                testId: 10,
                                isCountryProvidesTin: false,
                                firstReason: 'A',
                                secondReason: 'B',
                                validationResult: true,
                                tinVisible: false
                            });
                            fixedCountryValidationTest({
                                testId: 11,
                                isCountryProvidesTin: false,
                                firstReason: 'B',
                                secondReason: '',
                                validationResult: false,
                                tinVisible: false
                            });
                            fixedCountryValidationTest({
                                testId: 12,
                                isCountryProvidesTin: true,
                                firstReason: 'B',
                                secondReason: 'A',
                                validationResult: false,
                                tinVisible: false
                            });
                        });

                        describe('given the tin availability for the selected country is unclear', function() {
                            fixedCountryValidationTest({
                                testId: 13,
                                isCountryProvidesTin: null,
                                firstReason: '',
                                secondReason: 'A',
                                validationResult: true,
                                tinVisible: false
                            });
                            fixedCountryValidationTest({
                                testId: 14,
                                isCountryProvidesTin: null,
                                firstReason: '',
                                secondReason: 'B',
                                validationResult: true,
                                tinVisible: false
                            });
                            fixedCountryValidationTest({
                                testId: 15,
                                isCountryProvidesTin: null,
                                firstReason: 'A',
                                secondReason: '',
                                validationResult: true,
                                tinVisible: true
                            });
                            fixedCountryValidationTest({
                                testId: 16,
                                isCountryProvidesTin: null,
                                firstReason: 'A',
                                secondReason: 'B',
                                validationResult: true,
                                tinVisible: false
                            });
                            fixedCountryValidationTest({
                                testId: 17,
                                isCountryProvidesTin: null,
                                firstReason: 'B',
                                secondReason: '',
                                validationResult: true,
                                tinVisible: true
                            });
                            fixedCountryValidationTest({
                                testId: 18,
                                isCountryProvidesTin: null,
                                firstReason: 'B',
                                secondReason: 'A',
                                validationResult: true,
                                tinVisible: false
                            });
                        });
                    });

                    describe('changing country, fixed reason', function(){
                        var fixedCountryValidationTest = function(input){
                            describe(input.testId + ' - and first country ' + countryTinAvailabilityText(input.isFirstCountryProvidesTin), function() {
                                before(function(done) {
                                    $el = fixture('TestFixture');
                                    $el.initialData = data;
                                    if(input.isFirstCountryProvidesTin === true) {
                                        $el.selectedCountryIndex = 0;
                                    } else if(input.isFirstCountryProvidesTin === false) {
                                        $el.selectedCountryIndex = 1;
                                    } else {
                                        $el.selectedCountryIndex = 2;
                                    }
                                    flush(function(){
                                        if(input.reason === '') {
                                            $el.selectedReasonIndex = 0;
                                        } else if(input.reason === 'A'){
                                            $el.selectedReasonIndex = 1;
                                        }
                                        else if(input.reason === 'B'){
                                            $el.selectedReasonIndex = 2;
                                        }
                                        else {
                                            $el.selectedReasonIndex = 3;
                                        }
                                        flush(done);
                                    });
                                });

                                describe('when selected country changes to a country which ' + countryTinAvailabilityText(input.isSecondCountryProvidesTin) , function(){
                                    before(function(done){
                                        if(input.isSecondCountryProvidesTin === true) {
                                            $el.selectedCountryIndex = 0;
                                        } else if(input.isSecondCountryProvidesTin === false) {
                                            $el.selectedCountryIndex = 1;
                                        } else {
                                            $el.selectedCountryIndex = 2;
                                        }
                                        flush(done);
                                    });

                                    if(input.validationResult === true){
                                        it('then reason drop down validation should pass', function() {
                                            var validate = $el.$.reasonDropdown.validate();
                                            validate.should.be.true;
                                        });
                                    } else {
                                        it('then reason drop down validation should fail', function() {
                                            var validate = $el.$.reasonDropdown.validate();
                                            validate.should.be.false;
                                        });
                                    }

                                    if(input.tinVisible){
                                        it('and tin input should be visible', function() {
                                            expect($el.showTinOrEquivalentInput).to.be.true;
                                        });
                                    } else {
                                        it('and tin input should not be visible', function() {
                                            expect($el.showTinOrEquivalentInput).to.be.false;
                                        });
                                    }
                                });
                            });
                        }

                        var countryTinAvailabilityText = function(tinAvailability){
                            if (tinAvailability === true)
                                return 'provides tin';
                            if (tinAvailability === false)
                                return 'does not provide tin';
                            return 'tin availability is not clear';
                        }

                        describe('given the empty reason is selected', function() {
                            fixedCountryValidationTest({
                                testId: 1,
                                reason: '',
                                isFirstCountryProvidesTin: true,
                                isSecondCountryProvidesTin: false,
                                validationResult: false,
                                tinVisible: false
                            });

                            fixedCountryValidationTest({
                                testId: 2,
                                reason: '',
                                isFirstCountryProvidesTin: true,
                                isSecondCountryProvidesTin: null,
                                validationResult: true,
                                tinVisible: true
                            });

                            fixedCountryValidationTest({
                                testId: 3,
                                reason: '',
                                isFirstCountryProvidesTin: false,
                                isSecondCountryProvidesTin: true,
                                validationResult: true,
                                tinVisible: true
                            });

                            fixedCountryValidationTest({
                                testId: 4,
                                reason: '',
                                isFirstCountryProvidesTin: false,
                                isSecondCountryProvidesTin: null,
                                validationResult: true,
                                tinVisible: true
                            });

                        });

                        describe('given the reason A is selected', function() {
                            fixedCountryValidationTest({
                                testId: 5,
                                reason: 'A',
                                isFirstCountryProvidesTin: true,
                                isSecondCountryProvidesTin: false,
                                validationResult: true,
                                tinVisible: false
                            });

                            fixedCountryValidationTest({
                                testId: 6,
                                reason: 'A',
                                isFirstCountryProvidesTin: true,
                                isSecondCountryProvidesTin: null,
                                validationResult: true,
                                tinVisible: false
                            });

                            fixedCountryValidationTest({
                                testId: 7,
                                reason: 'A',
                                isFirstCountryProvidesTin: false,
                                isSecondCountryProvidesTin: true,
                                validationResult: false,
                                tinVisible: false
                            });

                            fixedCountryValidationTest({
                                testId: 8,
                                reason: 'A',
                                isFirstCountryProvidesTin: false,
                                isSecondCountryProvidesTin: null,
                                validationResult: true,
                                tinVisible: false
                            });

                        });

                        describe('given the reason B is selected', function() {
                            fixedCountryValidationTest({
                                testId: 9,
                                reason: 'B',
                                isFirstCountryProvidesTin: true,
                                isSecondCountryProvidesTin: false,
                                validationResult: true,
                                tinVisible: false
                            });

                            fixedCountryValidationTest({
                                testId: 10,
                                reason: 'B',
                                isFirstCountryProvidesTin: true,
                                isSecondCountryProvidesTin: null,
                                validationResult: true,
                                tinVisible: false
                            });

                            fixedCountryValidationTest({
                                testId: 11,
                                reason: 'B',
                                isFirstCountryProvidesTin: false,
                                isSecondCountryProvidesTin: true,
                                validationResult: true,
                                tinVisible: false
                            });

                            fixedCountryValidationTest({
                                testId: 12,
                                reason: 'B',
                                isFirstCountryProvidesTin: false,
                                isSecondCountryProvidesTin: null,
                                validationResult: true,
                                tinVisible: false
                            });

                        });
                    });

                    describe('changing country, fixed reason', function(){
                        describe('given the selected country provides tin', function() {
                            before(function(done) {
                                $el.reset();
                                $el.initialData = data;
                                $el.selectedCountryIndex = 0;
                                flush(done);
                            });

                            describe('and reason A is selected', function(){
                                before(function(done){
                                    $el.selectedReasonIndex = 1;
                                    flush(done);
                                });

                                describe('when the country is changed to another country which does not provide tin', function(){
                                    before(function(done){
                                        $el.selectedCountryIndex = 1;
                                        flush(done);
                                    });

                                    it("then reason drop down validation should pass", function() {
                                        var validate = $el.$.reasonDropdown.validate();
                                        validate.should.be.true;
                                    });
                                });
                            });
                        });

                        describe('given the selected country does not provide tin', function() {
                            before(function(done) {
                                $el.reset();
                                $el.initialData = data;
                                $el.selectedCountryIndex = 1;
                                flush(done);
                            });

                            describe('and reason A is selected', function(){
                                before(function(done){
                                    $el.selectedReasonIndex = 1;
                                    flush(done);
                                });

                                describe('when the country is changed to a country which provides tin', function(){
                                    before(function(done){
                                        $el.selectedCountryIndex = 0;
                                        flush(done);
                                    });

                                    it("then reason drop down validation should fail", function() {
                                        var validate = $el.$.reasonDropdown.validate();
                                        validate.should.be.false;
                                    });
                                });
                            });
                        });
                    });
                });

            });

            describe("texts", function() {
                it('countryDropdown label should not be empty', function() {
                    $el.$.countryDropdown.label.should.not.be.empty;
                });

                it('countryDropdown aria label should not be empty', function() {
                    $el.$.countryDropdown.ariaLabel.should.not.be.empty;
                });

                it('reasonDropdown label should not be empty', function() {
                    $el.$.reasonDropdown.label.should.not.be.empty;
                });

                it('reasonDropdown aria label should not be empty', function() {
                    $el.$.reasonDropdown.ariaLabel.should.not.be.empty;
                });

                it('tinOrEquivalentInput label should not be empty', function() {
                    $el.$.tinOrEquivalentInput.label.should.not.be.empty;
                });

                it('tinOrEquivalentInput label should not be empty', function() {
                    $el.$.tinOrEquivalentInput.placeHolderText.should.not.be.empty;
                });

                it('explanationInput aria label should not be empty', function() {
                    $el.$.explanationInput.ariaLabel.should.not.be.empty;
                });
            });

            describe("removeAustralia", function() {

                describe("when an empty array is passed to the function", function() {
                    it('should return an empty array', function() {
                        $el.removeAustralia([]).should.be.empty;
                    });
                });

                describe("when an undefined object is passed to the function", function() {
                    it('should return an empty array', function() {
                        $el.removeAustralia().should.be.empty;
                    });
                });

                describe("when an array containing Australia is passed to the function", function() {
                    var input = [
                        { "Code": "ABW", "Name": "ARUBA", "Code2": "AW", "EntityTinAvailable": false, "IndividualTinAvailable": false },
                        { "Code": "AUS", "Name": "AUSTRALIA", "Code2": "AU", "EntityTinAvailable": false, "IndividualTinAvailable": false }
                    ];

                    it("result array should not contain Australia", function() {
                        var output = $el.removeAustralia(input);                                                
                        output.length.should.equal(1);
                        output[0].Code.should.not.equal("AUS");
                    });

                    it("input array should stay untouched", function() {
                        var output = $el.removeAustralia(input);                                                
                        input.length.should.equal(2);
                    });
                });

                describe("when an array not containing Australia is passed to the function", function() {
                    var input = [
                        { "Code": "ABW", "Name": "ARUBA", "Code2": "AW", "EntityTinAvailable": false, "IndividualTinAvailable": false }
                    ];

                    it("result array stay untouched", function() {
                        var output = $el.removeAustralia(input);                                                
                        output.length.should.equal(1);
                        output[0].Code.should.equal("ABW");
                    });
                });
            });
        });
    </script>
</body>

</html>
_______________________________________________________________________________________________



js file

__________________________________________________

<link rel="import" href="../polymer/polymer/polymer.html">
<link rel="import" href="../underscore/underscore.html">
<link rel="import" href="../ing-dropdown/ing-dropdown.html">
<link rel="import" href="../ing-input/ing-input.html">
<link rel="import" href="../ing-textarea/ing-textarea.html">
<link rel="import" href="../ing-layout/ing-layout.html">
<link rel="import" href="./ing-crs-i18n.html">
<link rel="import" href="./ing-crs-country-behaviour.html">
<link rel="import" href="../polymer/iron-signals/iron-signals.html">

<dom-module id="ing-crs-tax-residence-country">
    <template>
        <style>
            @media only screen and (min-width: 767px) {
                #countryDropdown ::content iron-dropdown{
                    max-height: 198px;
                }
            }
            .tinNumericerror{
                color: #db1e31;
            }
        </style>
        <ing-globals id="globals" services="{{_globalServices}}"></ing-globals>
        <ing-crs-i18n id="i18n" content="{{i18n}}"></ing-crs-i18n>
        <ing-layout class="layout-2-a margin-bottom-m">
            <div class="col col-1">
                <ing-dropdown id="countryDropdown" label="{{i18n.taxResidence.country}}" place-holder-text="" aria-label="{{i18n.taxResidence.country}}"
                    selected="{{selectedCountryIndex}}" required>
                    <template is="dom-repeat" items="{{countries}}" as="country">
                        <paper-item>{{country.Name}}</paper-item>
                    </template>
                </ing-dropdown>
            </div>
            <div class="col col-2">
                <ing-dropdown id="reasonDropdown" label="{{i18n.taxResidence.reason}}" place-holder-text="" aria-label="{{i18n.taxResidence.reason}}" selected="{{selectedReasonIndex}}" required>
                    <template is="dom-repeat" items="{{reasons}}" as="reason">
                        <paper-item>{{reason.Description}}</paper-item>
                    </template>
                </ing-dropdown>
            </div>
        </ing-layout>
        <ing-layout class="layout-2-a margin-bottom-m" hidden$="[[!showTinOrEquivalentInput]]">
            <div class="col col-1">
                <ing-input id="tinOrEquivalentInput" label="{{i18n.taxResidence.tinOrEquivilentInThatCountry}}" place-holder-text="{{i18n.taxResidence.tinOrEquivilentInThatCountry}}" value="{{tin}}" required pattern="^[\s]*[\S][\S\s]{1,28}[\S][\s]*$" message-pattern-mismatch="[[i18n.errorMessages.tinValidation]]"
                    max-length="30" on-keypress= "_validateUSTin">
                </ing-input>
            </div>
        </ing-layout>
         <ing-layout class="layout-2-a margin-bottom-m tinNumericerror" hidden$="[[!countryTinNumericInput]]">
            <div class="col col-1">
                <span class="filter-container error options-error uia-error-message">
                    <span class="filter-message">[[i18n.taxResidence.countryTinNumericOnlyError]]</span>
                </span>
            </div>
        </ing-layout>
        <ing-layout class="margin-bottom-m tinNumericerror ing-layout-0" hidden$="[[!countryTinAvailabile]]">
            <div class="col col-1">
                <span class="filter-container error options-error uia-error-message">
                    <span class="filter-message">[[i18n.taxResidence.contryTinRequired]]</span>
                </span>
            </div>
        </ing-layout>
        <ing-layout class="layout-1 margin-bottom-m" hidden$="[[!showExplanationInput]]">
            <div class="col col-1">
                <p>[[i18n.taxResidence.explanationHint]]</p>
                <ing-textarea id="explanationInput" aria-label="{{i18n.taxResidence.explanationHint}}" value="{{explanation}}" displayContent="{{explanation}}" required max-length="100">
                </ing-textarea>
            </div>
        </ing-layout>
        <iron-signals on-iron-signal-ing-crs-tax-residence-saved="_onTaxResidenceSaved"></iron-signals>
    </template>
</dom-module>
<script>
    Polymer({
        is: 'ing-crs-tax-residence-country',
        properties: {
            _globalServices: Object,
            i18n: Object,
            countries: {
                type: Array,
                value: function() {
                    return [];
                }
            },
            selectedCountryCode: String,
            selectedCountryIndex: Number,
            reasons: {
                type: Array,
                value: function() {
                    return [];
                }
            },
            selectedReasonCode: String,
            selectedReasonIndex: Number,
            tin: String,
            explanation: String,
            showTinOrEquivalentInput: {
                type: Boolean,
                value: false
            },
            showExplanationInput: {
                type: Boolean,
                value: false
            },
            countryTinNumericInput:{
                type: Boolean,
                value: false 
            },
            countryTinAvailabile:{
                type: Boolean,
                value: false     
            },
            data: Object,
            initialData: Object,
            USACode: {
                type: String,
                readOnly: true,
                value: "US"
            }
        },
        behaviors: [INGCrsCountryBehaviour],
        observers: [
            '_selectedCountryIndexChaged(countries, selectedCountryIndex)',
            '_selectedReasonIndexChanged(reasons, selectedReasonIndex)',
            '_setInitialData(initialData)',
            '_setData(initialData, data)',
            '_validateUSTin(event)'
        ],
        ready: function() {
            this.$.countryDropdown.customValidator = this._validateUSResidence.bind(this);
            this.$.reasonDropdown.customValidator = this._validateReasonBasedOnCountry.bind(this);
        },
        reset: function() {
            this.selectedCountryCode = undefined;
            this.selectedCountryIndex = undefined;
            this.selectedReasonCode = undefined;
            this.selectedReasonIndex = undefined;
            this.tin = undefined;
            this.explanation = undefined;
            this.showTinOrEquivalentInput = false;
            this.showExplanationInput = false;
        },
        validate: function() {
            var country = this.$.countryDropdown;
            var reason = this.$.reasonDropdown;
            var tin = this.$$('#tinOrEquivalentInput');
            var explanation = this.$$('#explanationInput');

            country.validate();
            reason.validate();
            if (tin) tin.validate();
            if (explanation) explanation.validate();

            if (!country.isValid || !reason.isValid) return false;
            if(this.countries[this.selectedCountryIndex].Code === this.USACode) {
                if (this.selectedReasonCode === '' && (tin.value.length <9))
                this.countryTinNumericInput = true;
                return false;
            }
            if (this.selectedReasonCode === '') return tin.isValid;
            if (this.selectedReasonCode === 'B')  return false;
            if (this.selectedReasonCode === 'C') return false;
            
             return true; 
        },
        _setInitialData: function(initialData) {
            this.reset();
            this.countries = this.removeAustralia(initialData.Countries);
            this.reasons = initialData.TaxResidencyReasons;
        },
        _setData: function(initialData, data) {
            this.selectedCountryIndex = _.findIndex(this.countries, function(country) {
                return country.Code === data.Country;
            });
            this.selectedReasonIndex = _.findIndex(initialData.TaxResidencyReasons, function(reason) {
                return reason.Code === data.Reason;
            });
            this.tin = data.TIN;
            this.explanation = data.Explanation;
            if (this.explanation != undefined)
                this.$$('#explanationInput').$.textarea.value = this.explanation;
            else
                this.$$('#explanationInput').$.textarea.value = ''
        },
        _selectedCountryIndexChaged: function(countries, selectedCountryIndex) {
            if (selectedCountryIndex < 0) return;
            this.selectedCountryCode = countries[selectedCountryIndex].Code;
            if (this.selectedReasonIndex > -1 && this.selectedReasonIndex !== -1)
                this.$.reasonDropdown.validate();
        },
        _selectedReasonIndexChanged: function(reasons, selectedReasonIndex) {
                this.countryTinAvailabile = false;
            if (selectedReasonIndex < 0) return;
            this.selectedReasonCode = reasons[selectedReasonIndex].Code;
            if (this.selectedReasonCode === '') {
                this.showTinOrEquivalentInput = true;
            } else {
                this.showTinOrEquivalentInput = false;
                this.tin = undefined;
            }

            if (this.selectedReasonCode === 'B') {
                this.showExplanationInput = true;
                if (this.explanation !== undefined) {
                    this.$$('#explanationInput').$.textarea.value = this.explanation;
                }
            } else {
                this.showExplanationInput = false;
                this.explanation = undefined;
                this.$$('#explanationInput').$.textarea.value = '';
            }

                if(this.selectedReasonCode == 'B'){
                        this.showExplanationInput = false;
                        this.countryTinNumericInput = false;
                        this.countryTinAvailabile = true;
                }
                 if(this.selectedReasonCode == 'C'){
                        this.showExplanationInput = false;
                        this.countryTinNumericInput = false;
                        this.countryTinAvailabile = true;
                }
            

        },
        _validateUSResidence: function(selectedIndex) {
            if (!this.initialData.IsUsPerson && this.countries[selectedIndex].Code === this.USACode) {
                return this.i18n.taxResidence.providedUSResidencyError;
            }
        },
        _validateUSTin: function(event){
             if(typeof(this.selectedCountryIndex) !== "undefined" &&
                typeof(this.selectedReasonIndex) != "undefined"
                && this.countries[this.selectedCountryIndex].Code === this.USACode){
                var tin = event.target.value;
                     if(!!(event.target.value.length >=9)){
                        this.countryTinNumericInput = false;
                       event.preventDefault();
                       return false;
                       }
               if((event.charCode > 57) || (event.charCode < 48)){
                 this.countryTinNumericInput = true;
                event.preventDefault();
                return false;
               }
             this.set('tin', tin);
             this.countryTinNumericInput = false;
             if(event.target.value.length <9){
                this.countryTinNumericInput = true;
                return false;
             }
             return tin;
         }
        },
        _validateReasonBasedOnCountry: function(selectedIndex) {
            if (selectedIndex === 0 && this.selectedCountryIndex !== undefined && this.selectedCountryIndex !== -1 &&
            this.countries[this.selectedCountryIndex].TinAvailability === "N") {
                this.showTinOrEquivalentInput = false;
                return this.i18n.taxResidence.countryTinNotAvailabileError;
            }
            if (selectedIndex === 1 && this.selectedCountryIndex !== undefined && this.selectedCountryIndex !== -1 &&
                this.countries[this.selectedCountryIndex].TinAvailability === "Y") {
                this.showTinOrEquivalentInput = false;
                return this.i18n.taxResidence.countryTinAvailabileError;
            }
            if (selectedIndex === 0){
                this.showTinOrEquivalentInput = true;
            }
        },
        _onTaxResidenceSaved: function(e) {
            var selected = this.$.countryDropdown.selected;
            this.$.countryDropdown.clear();
            this.$.countryDropdown.selected = selected;
        }
    });

</script>


