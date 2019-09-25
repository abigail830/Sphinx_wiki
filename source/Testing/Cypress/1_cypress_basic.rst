Cypress Basic
======================

* `Basic Example`_
* `Key Commands for yield element - 获取控件`_
* `Key Commands for action - 触发动作`_
* `Key Commands for matcher - 匹配判断`_
* `Other Commands`_

Basic Example
-----------------

.. code-block:: javascript
  
  describe('Test conduit', function(){  
    it('Should able to access sign in page', function(){
        cy.visit('https://demo.realworld.io/#/')
        cy.contains('Sign in').click()
        cy.url().should('include', '/login')

        cy.get("[type='email']").type('abigail830@126.com')
        cy.get("[type='password']").type('!QAZ@WSX')
        cy.get('button').click()

        cy.contains('abigail830_126')
    })
  })
  
Key Commands for yield element - 获取控件 
-----------------------------------------------------

cy.get(element)
^^^^^^^^^^^^^^^^^^

https://docs.cypress.io/api/commands/get.html
  
.. code-block:: javascript
    
    cy.get('input').should('be.disabled') // by html tag
    cy.get('ul li:first').should('have.class', 'active') //by nest html tag
    cy.get('.dropdown-menu').click() // by class
    cy.get('[data-test-id="test-example"]').should('have.length', 5) //by attribute
    cy.get('button[type=submit]').as('submitBtn') //mix tag+attribute
    cy.get('ul>li').eq(4)  //get the 5th li in ul
    
    //within
    cy.get('form').within(() => {
      cy.get('input').type('Pamela') // Only yield inputs within form
      cy.get('textarea').type('is a developer') // Only yield textareas within form
    })

    //alias
    cy.get('ul#todos').as('todos')
    cy.get('@todos')


cy.contains(selector, content)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

https://docs.cypress.io/api/commands/contains.html, 获取优先级：input[type='submit'] < button < a < label

.. code-block:: javascript
    
    cy.contains('Hello')   //contains text
    cy.get('.nav').contains('About')  //combine used with get
    cy.get('#checkout-container').contains('Buy Now')
    
    //<form><input type="submit" value="submit the form!" /></form>
    //similar as cy.get('input[type=submit]')
    cy.get('form').contains('submit the form!').click()
    
    // yields <li>bananas</li>
    cy.contains(/^b\w+/)
    
cy.find(selector)
^^^^^^^^^^^^^^^^^^^^^^

https://docs.cypress.io/api/commands/find.html#Selector

.. code-block:: javascript
    
    cy.get('#parent').find('li')


Key Commands for action - 触发动作 
-----------------------------------------------------

.. code-block:: javascript
  
  //type
  cy.get('input').type('Hello, World') 
  
  //click
  cy.get('button').click() 
  cy.contains('Welcome').click() 
  
  //submit
  cy.get('form').submit() 
  
  //checkbox or radio | uncheck()
  cy.get('[type="radio"]').first().check() 
  
  //focus and blur
  cy.get('input').first().focus().blur()
  
  //scroll
  cy.scrollTo(0, 500)                     // Scroll the window 500px down
  cy.get('.sidebar').scrollTo('bottom')   // Scroll 'sidebar' to its bottom
  
  //mouseover
  cy.get('.menu-item').trigger('mouseover')


Key Commands for matcher - 匹配判断 
-----------------------------------------------------

.. code-block:: javascript
  
  cy.get('.error').should('be.empty')                    // Assert that '.error' is empty
  cy.contains('Login').should('be.visible')              // Assert that el is visible
  cy.wrap({ foo: 'bar' }).its('foo').should('eq', 'bar') // Assert the 'foo' property equals 'bar'
  
  //should+and
  cy.get('option:first').should('be.selected').and('have.value', 'Metallica')
  
  //seperate to body
  cy.get('.connectors-list > li').should(($lis) => {
    expect($lis).to.have.length(3)
    expect($lis.eq(0)).to.contain('Walk the dog')
  })
  
  //combine get+find+should+then
  cy.get('.docs-header')
    .find('div')
    // .should(cb) callback function will be retried
    .should(($div) => {
      expect($div).to.have.length(1)
    })
    // .then(cb) callback is not retried,
    // it either passes or fails
    .then(($div) => {
      expect($div).to.have.text('Introduction')
    })


**More matcher could refer to:**

* chai (https://github.com/chaijs/chai | https://www.chaijs.com/api/bdd/)
* sinon-chai (https://docs.cypress.io/guides/references/assertions.html#Sinon-Chai | https://github.com/domenic/sinon-chai)
* chai-jquery (https://docs.cypress.io/guides/references/assertions.html#Chai-jQuery)


Other Commands
-----------------------------------------------------

cy.visit(url)
^^^^^^^^^^^^^^^^^
访问URL


.. index:: Cypress, Testing
