5. Jest with React
==========================

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

Approach 1.a - UI Test 使用react自带的render
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


在运行下面测试后，目录中会生成_snapshots_文件夹，里面等于把界面渲染一次，转换成json然后存为.snap文件如下

.. code-block::
  
  // Jest Snapshot v1, https://goo.gl/fbAQLP

  exports[`Link changes the class when hovered 1`] = `
  <label>
    <input
      checked={false}
      onChange={[Function]}
      type="checkbox"
    />
    Off
  </label>
  `;

之后的每次运行，都会把component重新渲染和转换为json，然后与.snap中的内容比较，如果一致则通过，如果不一致（如本地界面确实做了修改），本地运行时会提示不一致，按U则可以接受变化并自动更新__snapshots__文件夹里面的内容。（_snapshot_及其内容需要一同提交到码云）

因为snapshot测试会渲染整个component涉及的页面，所以对覆盖率提升非常明显，写法也非常简单，但它并不能如上文UI Test那样验证页面内的逻辑操作，所以对logic部分的保护帮助较少。另一方面，如果页面稍有改动，snapshot就会需要更新（虽然更新也非常简单-只需要按U接受）。整体来说，考虑以UI测试为主，有余力的时候再顺手加个snapshot。

.. index:: Testing, Jest, React
