Cypress Basic
======================

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
  
Key Commands
-----------------

cy.get(element)
^^^^^^^^^^^^^^^^^^

获取控件 (https://docs.cypress.io/api/commands/get.html)
  
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

获取控件 (https://docs.cypress.io/api/commands/contains.html), 获取优先级：input[type='submit'] < button < a < label

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

获取(多个）控件 (https://docs.cypress.io/api/commands/find.html#Selector)

.. code-block:: javascript
    
    cy.get('#parent').find('li')



cy.visit(url)
^^^^^^^^^^^^^^^^^^^^^

访问URL





