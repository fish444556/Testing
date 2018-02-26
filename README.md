# Testing
The usage of different testing frameworks.

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
