# ts-binary-serializer
Binary serializer for typescript

## Quick Start

Install
```npm
npm install ts-binary-serializer --save
```

enable decorators on tsconfig.json
```tsconfig
"experimentalDecorators": true
```

```index.ts
import {BinaryDeserialize,BinarySerialize, seralizeField, DataType} from 'ts-binary-serializer';

class A{
    @SerializeField(DataType.String)
    public str:string = "HelloWorld";
}
let d = BinarySerialize(new A(),A); // [ 9, 0, 10, 72, 101, 108, 108, 111, 87, 111, 114, 108, 100 ]
let d1 = BinaryDeserialize(A,d); // A { str: 'HelloWorld' }
```

## Usage
```ts
//Serialize
export function BinarySerialize <T> (obj : T,type?:{new():T}):Uint8Array

//Deserialize
export function BinaryDeserialize<T>(type:{new():T},databuffer:Uint8Array): T |null
```

sample
```ts
classA{
//...
}

let a1 = new ClassA();
let data = BinarySerialize(a,ClassA);
let a2 = BinaryDeserialize(ClassA,data);
```

## Decorator

```ts
//Decorator @seralizeField
export function SerializeField(type : DataType,array:boolean = false,ptype?:any)

//All support DataType
//remark: all js [Number] type can be attached with strict typed [DataType],
//Use shorter [DataType] may have smaller binary serialization size.But result will be different if there has numeric overflow.
export enum DataType {
    Null,
    Float32,
    Float64,
    Int32,
    Int16,
    Int8,
    Uint32,
    Uint16
    Uint8,
    String,
    Bool,
    Object,
    Float16,
    Map,
    Varint32,
    UVarint32,
}

```

### Typed Array
set the second param of @seralizeField to true when the property is an Array object.
```ts
class ClassA{
    @SerializeField(DataType.String,true)
    public stringArray:string[];
    @SerializeField(DataType.Float32,true)
    public numAry:Array<Number>;
}
```
### Nested Class
```ts
class ClassB{
    @SerializeField(DataType.Bool)
    public valid:boolean;
}
class ClassC{
    @SerializeField(DataType.Object,false,ClassB)
    public b:ClassB;
}
// ...
let buffer = BinarySerialize(new ClassC())
```
nested class array is also supported.
```ts
class ClassC{
    @SerializeField(DataType.Object,true,ClassB)
    public b:ClassB[];
}
```
## Benchmark

simple benchmark

| type | Size(byte) | SerializeTime(ms) | DeserializeTime(ms) |
| --- | ---| --- | --- |
| Json | 641395 | 7.0295 | 8.5726 |
| Binary | 157420 | 19.1871 | 19.8457 |


## License

MIT
