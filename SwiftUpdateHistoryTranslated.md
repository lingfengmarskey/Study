##### 更新记录

### 2019-03-25

* 更新到Swift 5.0

* 在字符串这一部分中,添加了关于分隔符的延展功能

	```
	let multiplelinestring = """
	hello, world,
	\n is just a character.
	
	include a blank line above,and anather blank line below
	
	and\
	can use a just a writing separetation,but no on the result
	"""
	
	let anatherMultiplelinestring = ###
	here it is.\#n can be a reguler change line func symbol
	###
	
	```
		
* 新增了一个特性,叫可动态调用(dynamicCallable),使用它可以把一个实例作为一个函数来动态调用.
 
	```
	1.可以用来修饰class,structure,protocol 
	2.被修饰的类型,必须实现以下任意一个方法
		// 该方法的参数 必须遵循 ExpressibleByArrayLiteral protocol
		1 dynamicallyCall(withArguments:)
		
		// 该方法的参数 必须遵循 ExpressibleByDictionaryLiteral protocol
		// 同时 key 必须遵循 ExpressibleByStringLiteral protocol
		2 dynamicallyCall(withKeywordArguments:)	

		//e.g

		@dynamicCallable
		struct Repeater {
		    func dynamicallyCall(withKeywordArguments pairs: KeyValuePairs<String, Int>) -> String {
		        return pairs
		            .map { label, count in
		                repeatElement(label, count: count).joined(separator: " ")
		            }
		            .joined(separator: "\n")
		    }
		}
		
		let repeatLabels = Repeater()
		print(repeatLabels(a: 1, b: 2, c: 3, b: 2, a: 1))
		// a
		// b b
		// c c c
		// b b
		// a
	
	```	
	
* 新增了 Switch Case 的特性 'unknown'

	```
	enumeration 有2种类型
	1.不可变(frozen)枚举,以后也不会出现任何新case
	2.可变的(nonfrozen)枚举,以后可能会有新的case出现
	
	当前用swift自定义的enum都是 不可变的
	在系统的枚举类型中,可能存在这种情况
	为了穷举,避免所有情况都考虑到,并且,在系统更新新case的时候,代码中有提示,加入此特性
	
	// e.g
	
	let representation: Mirror.AncestorRepresentation = .generated
	switch representation {
	case .customized:
	    print("Use the nearest ancestor’s implementation.")
	case .generated:
	    print("Generate a default mirror for all ancestor classes.")
	case .suppressed:
	    print("Suppress the representation of all ancestor classes.")
	@unknown default:
	    print("Use a representation that was unknown when this code was compiled.")
	}
	// Prints "Generate a default mirror for all ancestor classes."


	
	```
	
* 新增了 关于 KeyPath 表达式的特性 keyPath(.\self)

	```
	通过 key-path表达式,可以引用 属性或者类型的下标
	标准形式是:\type name.path
	可以通过此方式 来修改 或者读取相关值
	
	其他形式:  
		1.-> \.path			// 对应的path
		2.-> \.self.path		// 对应的path
		3.-> \.self			// 对应self本身
	
	// e.g
	
	let greetings = ["hello", "hola", "bonjour", "안녕"]
	let myGreeting = greetings[keyPath: [String]\.[1]]
	
	//	let greetString = greetings[keyPath: \.self.[1]] 
	// complie error ,can not access subscript using self keyword
	
	let greet = greetings[keyPath: \.self]
	
	// myGreeting is "hola"
	// greet is equal array greetigns
	
	
	```
	
* 在编译条件中 关于平台判断条件 新增了 小于(<) 操作符

	```
	
	// e.g
	
	// #if swift(<5)

	// do something

	// #endif
	
	```
	|Platform condition|Valid arguments|	
	|---|---|
	|os()|macOS, iOS, watchOS, tvOS, Linux|
	|arch()|i386, x86_64, arm, arm64|
	|swift()|>= or < followed by a version number|
	|compiler()|>= or < followed by a version number|
	|canImport()|A module name|	
