Cypress Basic
======================

.. code-block:: javascript
  
  describe('Test conduit', function(){  
    it('Should able to access the conduit demo website', function(){
        cy.visit('https://demo.realworld.io/#/')
    })

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
  
