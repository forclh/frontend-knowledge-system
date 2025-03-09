[官方文档](https://docs.cypress.io/app/get-started/why-cypress)


## 通用函数

```js
/**
 * 表单填写
 * @param {object} fields 表单字段及值
 * cy.fillForm({
      '[data-testid="contact-name"]': '测试',
      '[data-testid="contact-phone"]': '13800138000'
    })
 */
Cypress.Commands.add('fillForm', (fields) => {
  Object.entries(fields).forEach(([selector, value]) => {
    cy.get(selector).clear().type(value)
  })
})
```