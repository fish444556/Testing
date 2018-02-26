# Testing
The usage of different testing frameworks.


## table-of-contents
### enzyme
<details>
<summary>View contents</summary>

* [`counter`](#counter)
</details>

### Jasmine
<details>
<summary>View contents</summary>

* [`phone-list`](#phone-list)
</details>

### Karma
<details>
<summary>View contents</summary>

* [`angular-phonecat`](#angular-phonecat)
</details>



### [counter](https://github.com/reactjs/redux/tree/master/examples/counter)
```js
import React from 'react'

import { shallow } from 'enzyme'
import Counter from './Counter'

function setup(value = 0) {
  const actions = {
    onIncrement: jest.fn(),
    onDecrement: jest.fn()
  }
  const component = shallow(
    <Counter value={value} {...actions} />
  )

  return {
    component: component,
    actions: actions,
    buttons: component.find('button'),
    p: component.find('p')
  }
}

describe('Counter component', () => {
  it('should display count', () => {
    const { p } = setup()
    expect(p.text()).toMatch(/^Clicked: 0 times/)
  })

  it('first button should call onIncrement', () => {
    const { buttons, actions } = setup()
    buttons.at(0).simulate('click')
    expect(actions.onIncrement).toBeCalled()
  })

  it('second button should call onDecrement', () => {
    const { buttons, actions } = setup()
    buttons.at(1).simulate('click')
    expect(actions.onDecrement).toBeCalled()
  })

  it('third button should not call onIncrement if the counter is even', () => {
    const { buttons, actions } = setup(42)
    buttons.at(2).simulate('click')
    expect(actions.onIncrement).not.toBeCalled()
  })

  it('third button should call onIncrement if the counter is odd', () => {
    const { buttons, actions } = setup(43)
    buttons.at(2).simulate('click')
    expect(actions.onIncrement).toBeCalled()
  })

  it('third button should call onIncrement if the counter is odd and negative', () => {
    const { buttons, actions } = setup(-43)
    buttons.at(2).simulate('click')
    expect(actions.onIncrement).toBeCalled()
  })

  it('fourth button should call onIncrement in a second', (done) => {
    const { buttons, actions } = setup()
    buttons.at(3).simulate('click')
    setTimeout(() => {
      expect(actions.onIncrement).toBeCalled()
      done()
    }, 1000)
  })
})
```
<br>[⬆ Back to top](#table-of-contents)


### [phone-list](https://github.com/angular/angular-phonecat/blob/master/app/phone-list/phone-list.component.spec.js)
```js
'use strict';

describe('phoneList', function() {

  // Load the module that contains the `phoneList` component before each test
  beforeEach(module('phoneList'));

  // Test the controller
  describe('PhoneListController', function() {
    var $httpBackend, ctrl;

    beforeEach(inject(function($componentController, _$httpBackend_) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/phones.json')
                  .respond([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);

      ctrl = $componentController('phoneList');
    }));

    it('should create a `phones` property with 2 phones fetched with `$http`', function() {
      jasmine.addCustomEqualityTester(angular.equals);

      expect(ctrl.phones).toEqual([]);

      $httpBackend.flush();
      expect(ctrl.phones).toEqual([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);
    });

    it('should set a default value for the `orderProp` property', function() {
      expect(ctrl.orderProp).toBe('age');
    });

  });

});
```
<br>[⬆ Back to top](#table-of-contents)



### [angular-phonecat](https://github.com/angular/angular-phonecat/blob/master/e2e-tests/scenarios.js)
```js
'use strict';

// Angular E2E Testing Guide:
// https://docs.angularjs.org/guide/e2e-testing

describe('PhoneCat Application', function() {

  it('should redirect `index.html` to `index.html#!/phones', function() {
    browser.get('index.html');
    expect(browser.getLocationAbsUrl()).toBe('/phones');
  });

  describe('View: Phone list', function() {

    beforeEach(function() {
      browser.get('index.html#!/phones');
    });

    it('should filter the phone list as a user types into the search box', function() {
      var phoneList = element.all(by.repeater('phone in $ctrl.phones'));
      var query = element(by.model('$ctrl.query'));

      expect(phoneList.count()).toBe(20);

      query.sendKeys('nexus');
      expect(phoneList.count()).toBe(1);

      query.clear();
      query.sendKeys('motorola');
      expect(phoneList.count()).toBe(8);
    });

    it('should be possible to control phone order via the drop-down menu', function() {
      var queryField = element(by.model('$ctrl.query'));
      var orderSelect = element(by.model('$ctrl.orderProp'));
      var nameOption = orderSelect.element(by.css('option[value="name"]'));
      var phoneNameColumn = element.all(by.repeater('phone in $ctrl.phones').column('phone.name'));

      function getNames() {
        return phoneNameColumn.map(function(elem) {
          return elem.getText();
        });
      }

      queryField.sendKeys('tablet');   // Let's narrow the dataset to make the assertions shorter

      expect(getNames()).toEqual([
        'Motorola XOOM\u2122 with Wi-Fi',
        'MOTOROLA XOOM\u2122'
      ]);

      nameOption.click();

      expect(getNames()).toEqual([
        'MOTOROLA XOOM\u2122',
        'Motorola XOOM\u2122 with Wi-Fi'
      ]);
    });

    it('should render phone specific links', function() {
      var query = element(by.model('$ctrl.query'));
      query.sendKeys('nexus');

      element.all(by.css('.phones li a')).first().click();
      expect(browser.getLocationAbsUrl()).toBe('/phones/nexus-s');
    });

  });

  describe('View: Phone detail', function() {

    beforeEach(function() {
      browser.get('index.html#!/phones/nexus-s');
    });

    it('should display the `nexus-s` page', function() {
      expect(element(by.binding('$ctrl.phone.name')).getText()).toBe('Nexus S');
    });

    it('should display the first phone image as the main phone image', function() {
      var mainImage = element(by.css('img.phone.selected'));

      expect(mainImage.getAttribute('src')).toMatch(/img\/phones\/nexus-s.0.jpg/);
    });

    it('should swap the main image when clicking on a thumbnail image', function() {
      var mainImage = element(by.css('img.phone.selected'));
      var thumbnails = element.all(by.css('.phone-thumbs img'));

      thumbnails.get(2).click();
      expect(mainImage.getAttribute('src')).toMatch(/img\/phones\/nexus-s.2.jpg/);

      thumbnails.get(0).click();
      expect(mainImage.getAttribute('src')).toMatch(/img\/phones\/nexus-s.0.jpg/);
    });

  });

});
```
<br>[⬆ Back to top](#table-of-contents)
