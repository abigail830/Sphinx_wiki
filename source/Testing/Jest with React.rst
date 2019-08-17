Jest with React
==========================

如果分层结构解耦得比较好，逻辑和视图的测试是可以分开进行的。对于逻辑的测试，可以参考 `Jest Basic  <http://wiki.saraqian.cn/Testing/Jest.html>`_ , 以下的例子主要展示视图层的测试.

当使用Jest为测试框架，测试可以以两种方式进行: UI Test / Snapshot Test

下面以其中一个CheckBox组件为例子, Checkbox上有label，会随着点击在On/Off之间切换

.. code-block:: javascript

  import React from 'react';
  
  export default class CheckboxWithLabel extends React.Component {
    constructor(props) {
      super(props);
      this.state = {isChecked: false};
      this.onChange = this.onChange.bind(this);
  }

  onChange() {
    this.setState({isChecked: !this.state.isChecked});
  }

  render() {
    return (
      <label>
        <input
          type="checkbox"
          checked={this.state.isChecked}
          onChange={this.onChange}
        />
        {this.state.isChecked ? this.props.labelOn : this.props.labelOff}
      </label>
    );
    }
  }

当我们想对这个组件进行测试

Approach 1.a - UI Test 使用react原声render
--------------------------------------------------

.. code-block:: javascript
  
  import React from 'react';
  import {render, fireEvent, cleanup} from '@testing-library/react';
  import CheckboxWithLabel from '../CheckboxWithLabel';

  // automatically unmount and cleanup DOM after the test is finished.
  afterEach(cleanup);

  test('CheckboxWithLabel should change the text after click', () => {
    //given
    const {queryByLabelText, getByLabelText} = render(
      <CheckboxWithLabel labelOn="On" labelOff="Off" />,
    );
    expect(queryByLabelText(/off/i)).toBeTruthy();

    //when
    fireEvent.click(getByLabelText(/off/i));
  
    //then
    expect(queryByLabelText(/on/i)).toBeTruthy();
  });

Approach 1.b - UI Test 使用enzyme的render
--------------------------------------------------

.. code-block:: javascript
  
  import React from 'react';
  import {shallow,configure} from 'enzyme';
  import CheckboxWithLabel from '../CheckboxWithLabel';
  import Adapter from 'enzyme-adapter-react-16';

  configure({adapter: new Adapter()});

  test('CheckboxWithLabel changes the text after click', () => {
    // Render a checkbox with label in the document
    const checkbox = shallow(<CheckboxWithLabel labelOn="On" labelOff="Off" />);

    expect(checkbox.text()).toEqual('Off');

    checkbox.find('input').simulate('change');

    expect(checkbox.text()).toEqual('On');
  });



Approach 2 - Snapshot Test
----------------------------------

.. code-block:: javascript
  
  // Link.react.test.js
  import React from 'react';
  import CheckboxWithLabel from '../CheckboxWithLabel';

  //react-test-render could help to bypass the warning if component name in big char
  import renderer from 'react-test-renderer';

  test('Link changes the class when hovered', () => {
    const component = renderer.create(
      <CheckboxWithLabel labelOn="On" labelOff="Off" />,
    );

    let tree = component.toJSON();
    expect(tree).toMatchSnapshot();
  });
