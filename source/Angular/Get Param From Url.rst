Get Param From URL
=======================

假设：

* 有dashboard页面： http://localhost:4200/dashboard
* 点击列表任意一项，会带着对应ID跳进detail页面：http://localhost:4200/pokemon-detail/003

问：

* 如何获取URL上面的ID？

Solution：

.. code-block:: typescript
  :linenos:
  :emphasize-lines: 2,11,14
  
  import { Component, OnInit } from '@angular/core';
  import { ActivatedRoute } from '@angular/router';
  
  @Component({
    selector: 'app-pokemon-detail',
    templateUrl: './pokemon-detail.component.html',
    styleUrls: ['./pokemon-detail.component.scss']
  })
  export class PokemonDetailComponent implements OnInit {

    id: string;
    constructor(private route: ActivatedRoute) { }

    ngOnInit() {
      this.id = this.route.snapshot.paramMap.get('id');
    }
}
