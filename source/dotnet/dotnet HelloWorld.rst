.net Hello world
=========================

Install SDK
^^^^^^^^^^^^^^^^^
Refer to https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install

Create project via CLI
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash
  dotnet new console -o myApp

.. code-block:: c#
  using System;

  namespace myApp
  {
      class Program
      {
          static void Main(string[] args)
          {
              Console.WriteLine("Hello World!");
          }
      }
  }
  
Run project via CLI
^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash
  dotnet run
  


