1.1 Update to Jest
======================

Step1. clean up Karma in default Angular project

.. code-block:: bash
  
  npm remove karma karma-chrome-launcher karma-coverage-istanbul-reporter karma-jasmine karma-jasmine-html-reporter
  rm ./karma.conf.js 
  rm ./src/test.ts


Step2. add the dependency of Jest:

.. code-block:: bash
  
  npm install --save-dev jest jest-preset-angular @types/jest
  #npm install --save-dev jest @types/jest @angular-builders/jest

Step3. Update tsconfig files

* In tsconfig.spec.json, replace 'jasmine' to 'jest' in types, remove "src/test.ts" under files.
* In tsconfig.json, add types with jest to compilerOptions as well

.. code-block:: json
  
  {
    "extends": "./tsconfig.json",
    "compilerOptions": {
      "outDir": "./out-tsc/spec",
      "types": [
        "jest",
        "node"
      ]
    },
    "files": [
      "src/polyfills.ts"
    ],
    "include": [
      "src/**/*.spec.ts",
      "src/**/*.d.ts"
    ]
  }

Step4. Update angular.json
 
Replace test builder:
 
* from "builder": "@angular-devkit/build-angular:karma",' 
* to '"builder": "@angular-builders/jest:run"'
* feel free to remove karma config and main config
 
Step5. jest-config.js
 
This is the config included test coverage, otherwise, could also only use first 3 lines.

.. code-block:: javascript
  
  module.exports = {
    preset: "jest-preset-angular",
    roots: ['src'],
    setupFilesAfterEnv: "<rootDir>/src/setup-jest.ts",
    transformIgnorePatterns: [
        "node_modules/(?!(@ionic-native|@ionic|angularfire2)/)"
    ],
    collectCoverage: true,
    collectCoverageFrom: [
        "src/**/*.ts"
    ],
    coverageDirectory: "<rootDir>/coverage/",
    coveragePathIgnorePatterns: [
        "node_modules",
        "test-config",
        "<rootDir>/src/app/models",
        "jestGlobalMocks.ts",
        ".module.ts",
        "<rootDir>/src/main.ts",
        "<rootDir>/src/polyfills.ts"
    ],
  }
 
 
 
