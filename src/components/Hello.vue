<template>
    <div>
        <h1>{{ msg }}</h1>
        <h1>{{ name }}</h1>
        <p>
            <input type="text" placeholder="输入特性名称" @keyup.enter="addFeatures"/>
        </p>
        <ul>
            <li v-for="f in features" :key="f.id"> {{ f.name }} </li>
            <li>{{ featureCount }}</li>
        </ul>
    </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Emit, Watch } from "vue-property-decorator";

// 类型注解
let name: string;
const title = 'xx'; // 类型推论
name = 'gaga';

// 数组类型
// let names: Array<string>;
let names: string[];
names = ['tom', 'jerry'];

// 任意类型
let list: any[];
list = [1, 'string', true];

// 在函数中使用类型约束
function greeting(person: string):string {
    return "hello," + person;
}

greeting('tom');

// void类型
function warn():void {
    alert("warning!!!!");
}

// 内置类型： string, number, boolean, void, any

// ts函数中如果声明且没有特殊符号，就是必选参数,如果指定了默认值，则可以不传
function sayHello(name: string, age:number=20):string {
    return name + ' ' + age;
}

// 尾缀'?'表示参数可选
function sayHello2(name: string, age?:number):string {
    return name + ' ' + age;
}

// 函数重载，多个同名函数，通过参数数量或者类型不同或者返回值不同
// ts中函数重载需要先申明再实现
function info(a:{ name: string }):string;
function info(a:string):object;
function info(a:any):any {
    if (typeof a === 'object') {
        return a.name;
    } else {
        return { name: a }
    }
}
info({ name: "sherily" })
info("jerry")

sayHello('ccc', 20);
sayHello2('ccc')

export class Feature {
    constructor( public id: number, public name: string, public version: string) {}
}
// 装饰器: 实际上是工厂函数，传入一个对象，输出处理后的新对象。
// 若不加小括号，则装饰器下面紧挨着的对象就是目标对象
@Component({
    props: {
        // 属性可以在这里配置
        sname: {
            type: String,
            default: ''
        }
    }
})
export default class Hello extends Vue {
  // private 仅当前类可用
  // protected 子类也可以用
  // public 外部可用
  // readonly: 只读属性必须在声明时或者构造函数里被初始化
  // 参数属性： 给构造函数的参数加上修饰符，能够定义并初始化一个成员属性
  // 存取器： 当获取和设置属性时有额外逻辑时可以使用存取器(getter,setter)
  @Prop() private msg!: string; // ‘!’表示属性msg为必填项，字符串类型
  @Prop({default: 'hihi~'}) private name?: string; // '?'表示name属性为可选项
//   @Prop() private obj: {foo: string}; // 限制父类型


  // 普通的属性相当于组件中data中的数据
//   private features: Feature[]= [
//       {id: 1, name: '静态类型', version: '1.0'},
//       {id: 2, name: '编译', version: '1.0'},
//     ];
  private features: Feature[] = [];

  @Emit()
  addFeatures(event: any) {
    console.log(event);
    // 若没有返回值形参将作为事件参数
    const feature = {
        name: event.target.value,
        id: this.features.length + 1,
        version: "1.0"
    };
    this.features.push(feature);
    // this.features.push({name: event.target.value, id: this.features.length + 1, version: '1.0'});
    event.target.value = '';
    return feature; // 返回值作为事件参数
  }

  @Watch('msg')
  onRouteChange(val: string, oldVal: any) {
    console.log(val, oldVal);
  }




  // 生命周期
  async created() {
      // ...
      const result = await getData<Feature>();
      this.features = result.data;
  }
  // 计算属性
  get featureCount() {
      return this.features.length;
  }
}


class Shape {
    readonly foo: string = "foo";
    area: number;
    // protected color: string;

    constructor ( protected color: string, width: number, height: number ) {
        this.area = width * height;
        // this.color = color;
    }

    shoutout() {
        return "I'm" + this.color + "with an area of" + this.area + "cm squared."
    }
}

class Square extends Shape {
    constructor( color: string, side: number ) {
        super(color, side, side);
        console.log(this.color);
    }

    shoutout() {
        return "我是" + this.color + "面积是" + this.area + "平方厘米";
    }

}

const square: Square = new Square('blue', 2)
console.log(square.shoutout());


class Employee {
    // private _fullName: string = 'Mike James';
    private _firstName: string = "Mike";
    private _lastName: string = "James";

    get fullName(): string {
        return this._firstName +  ' ' + this._lastName;
    }
    set fullName(newName: string) {
        console.log("您修改了用户名");
        this._firstName = newName.split(' ')[0];
        this._lastName = newName.split(' ')[1];
    }
}
const employee = new Employee();
employee.fullName = 'Bob Smith';
console.log(employee);


// 接口仅约束结构，不要求实现，使用更简单
interface PersonFormat {
    firstName: string;
    lastName: string;
    sayHello(): string;
}
class Greeter implements PersonFormat {
    constructor( public firstName = " ", public lastName = " " ) {}
    sayHello() {
        return 'Hello,' + this.firstName + ' ' + this.lastName
    }
}
function greeting2(person: PersonFormat) {
    return 'Hello,' + person.firstName + ' ' + person.lastName;
}
const user = { firstName: "Jane", lastName: "Junes" , sayHello: () => 'hehehehehe'};
const user2 = new Greeter("a", "b");


console.log(user);
console.log(greeting2(user));
console.log(greeting2(user2));

// 泛型(Generics)是指在定义函数、接口或者类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。
interface Result<T> {
    ok: 0 | 1;
    data: T[];
}

// 泛型函数
function getData<T>(): Promise<Result<T>> {
    const data: any[] = [
        {id: 1, name: '静态类型', version: '1.0'},
        {id: 2, name: '编译', version: '1.0'},
    ];
    return Promise.resolve({ ok: 1, data } as Result<T>);
}

</script>

<style scoped>

</style>