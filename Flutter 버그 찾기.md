Flutter에서 자주 발생하는 버그가 포함된 코드 예시를 몇 가지 준비해드리겠습니다. 각 문제를 살펴보고 버그를 찾아 해결해보세요.

### 문제 1: 비동기 데이터 로딩 버그
```dart
class DataDisplayWidget extends StatefulWidget {
  @override
  _DataDisplayWidgetState createState() => _DataDisplayWidgetState();
}

class _DataDisplayWidgetState extends State<DataDisplayWidget> {
  List<String> items = [];
  bool isLoading = false;

  void fetchData() async {
    isLoading = true;
    // 데이터를 비동기적으로 가져오는 작업 (API 호출 등)
    await Future.delayed(Duration(seconds: 2));
    items = ['Item 1', 'Item 2', 'Item 3'];
    isLoading = false;
  }

  @override
  void initState() {
    super.initState();
    fetchData();
  }

  @override
  Widget build(BuildContext context) {
    return isLoading
        ? CircularProgressIndicator()
        : ListView.builder(
            itemCount: items.length,
            itemBuilder: (context, index) => ListTile(
              title: Text(items[index]),
            ),
          );
  }
}
```

### 문제 2: 빌드 시 상태 변경 버그
```dart
class AutoUpdateWidget extends StatefulWidget {
  @override
  _AutoUpdateWidgetState createState() => _AutoUpdateWidgetState();
}

class _AutoUpdateWidgetState extends State<AutoUpdateWidget> {
  String message = "초기 메시지";

  @override
  Widget build(BuildContext context) {
    updateMessage();
    
    return Center(
      child: Text(message, style: TextStyle(fontSize: 24)),
    );
  }

  void updateMessage() {
    setState(() {
      message = "업데이트된 메시지: ${DateTime.now().second}";
    });
  }
}
```

### 문제 3: 리스트 아이템 선택 버그
```dart
class SelectableListWidget extends StatefulWidget {
  @override
  _SelectableListWidgetState createState() => _SelectableListWidgetState();
}

class _SelectableListWidgetState extends State<SelectableListWidget> {
  final List<String> items = ['항목 1', '항목 2', '항목 3', '항목 4', '항목 5'];
  int selectedIndex = -1;

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: items.length,
      itemBuilder: (context, index) {
        return ListTile(
          title: Text(items[index]),
          tileColor: selectedIndex == index ? Colors.blue[100] : null,
          onTap: () {
            selectedIndex = index;
          },
        );
      },
    );
  }
}
```

### 문제 4: TextField 컨트롤러 버그
```dart
class SearchWidget extends StatefulWidget {
  @override
  _SearchWidgetState createState() => _SearchWidgetState();
}

class _SearchWidgetState extends State<SearchWidget> {
  final TextEditingController controller = TextEditingController();
  String searchResult = "";

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        TextField(
          controller: controller,
          decoration: InputDecoration(
            labelText: '검색어 입력',
            suffixIcon: IconButton(
              icon: Icon(Icons.search),
              onPressed: () {
                searchResult = "검색 결과: ${controller.text}";
              },
            ),
          ),
        ),
        SizedBox(height: 20),
        Text(searchResult),
      ],
    );
  }
}
```

### 문제 5: 애니메이션 버그
```dart
class AnimatedBoxWidget extends StatefulWidget {
  @override
  _AnimatedBoxWidgetState createState() => _AnimatedBoxWidgetState();
}

class _AnimatedBoxWidgetState extends State<AnimatedBoxWidget> {
  double boxSize = 100.0;

  void toggleSize() {
    boxSize = boxSize == 100.0 ? 200.0 : 100.0;
  }

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          AnimatedContainer(
            duration: Duration(milliseconds: 300),
            width: boxSize,
            height: boxSize,
            color: Colors.blue,
          ),
          SizedBox(height: 20),
          ElevatedButton(
            onPressed: toggleSize,
            child: Text('크기 변경'),
          ),
        ],
      ),
    );
  }
}
```

### 문제 6: 이미지 로딩 메모리 누수 버그
```dart
class ImagePreviewWidget extends StatefulWidget {
  final String imageUrl;
  
  const ImagePreviewWidget({Key? key, required this.imageUrl}) : super(key: key);
  
  @override
  _ImagePreviewWidgetState createState() => _ImagePreviewWidgetState();
}

class _ImagePreviewWidgetState extends State<ImagePreviewWidget> {
  late final NetworkImage _image;
  
  @override
  void initState() {
    super.initState();
    _image = NetworkImage(widget.imageUrl);
    precacheImage(_image, context);
  }
  
  @override
  Widget build(BuildContext context) {
    return Container(
      width: 300,
      height: 200,
      decoration: BoxDecoration(
        image: DecorationImage(
          image: _image,
          fit: BoxFit.cover,
        ),
      ),
    );
  }
}
```

### 문제 7: ListView 성능 버그
```dart
class InfiniteListWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ListView(
      children: List.generate(10000, (index) {
        return ListTile(
          leading: CircleAvatar(
            backgroundImage: NetworkImage('https://via.placeholder.com/50'),
          ),
          title: Text('항목 $index'),
          subtitle: Text('상세 설명입니다.'),
          trailing: Icon(Icons.arrow_forward_ios),
          onTap: () {
            print('항목 $index 선택됨');
          },
        );
      }),
    );
  }
}
```

### 문제 8: 화면 전환 데이터 전달 버그
```dart
class MainScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('메인 화면')),
      body: Center(
        child: ElevatedButton(
          child: Text('상세 화면으로 이동'),
          onPressed: () {
            // 사용자 정보를 생성
            Map<String, dynamic> user = {
              'name': '홍길동',
              'age': 30,
              'isActive': true
            };
            
            Navigator.push(
              context,
              MaterialPageRoute(
                builder: (context) => DetailScreen(),
              ),
            );
          },
        ),
      ),
    );
  }
}

class DetailScreen extends StatelessWidget {
  final Map<String, dynamic> userData;
  
  const DetailScreen({Key? key, required this.userData}) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('상세 화면')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text('이름: ${userData['name']}'),
            Text('나이: ${userData['age']}'),
            Text('활성 상태: ${userData['isActive'] ? '활성' : '비활성'}'),
          ],
        ),
      ),
    );
  }
}
```

### 문제 9: Context 사용 버그
```dart
class ContextErrorWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Context 사용 오류')),
      body: Center(
        child: ElevatedButton(
          child: Text('다이얼로그 표시'),
          onPressed: () => _showDialog(),
        ),
      ),
    );
  }

  Future<void> _showDialog() async {
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: Text('알림'),
        content: Text('다이얼로그 내용입니다.'),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context),
            child: Text('확인'),
          ),
        ],
      ),
    );
  }
}
```

### 문제 10: 위젯 렌더링 사이즈 버그
```dart
class ResponsiveLayoutWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('반응형 레이아웃')),
      body: Row(
        children: [
          Container(
            color: Colors.blue,
            width: 150,
            child: ListView(
              children: List.generate(20, (index) => 
                ListTile(title: Text('메뉴 $index')),
              ),
            ),
          ),
          Column(
            children: [
              Container(
                color: Colors.amber,
                height: 200,
                child: Center(
                  child: Text('상단 콘텐츠 영역'),
                ),
              ),
              Container(
                color: Colors.green,
                height: 300,
                child: Center(
                  child: Text('하단 콘텐츠 영역'),
                ),
              ),
            ],
          ),
        ],
      ),
    );
  }
}
```

### 문제 11: StatefulWidget 상태 초기화 버그
```dart
class CounterPage extends StatefulWidget {
  final int initialCount;
  
  const CounterPage({Key? key, this.initialCount = 0}) : super(key: key);
  
  @override
  _CounterPageState createState() => _CounterPageState();
}

class _CounterPageState extends State<CounterPage> {
  int count = 0;
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('카운터')),
      body: Center(
        child: Text(
          '$count',
          style: TextStyle(fontSize: 48),
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          setState(() {
            count++;
          });
        },
        child: Icon(Icons.add),
      ),
    );
  }
}
```

### 문제 12: StreamBuilder 구독 해제 버그
```dart
class StockPriceWidget extends StatefulWidget {
  @override
  _StockPriceWidgetState createState() => _StockPriceWidgetState();
}

class _StockPriceWidgetState extends State<StockPriceWidget> {
  final Stream<int> _priceStream = Stream.periodic(
    Duration(seconds: 1), 
    (count) => 100 + (count % 20)
  );
  
  @override
  Widget build(BuildContext context) {
    return StreamBuilder<int>(
      stream: _priceStream,
      builder: (context, snapshot) {
        if (snapshot.hasData) {
          return Text(
            '현재 주가: ${snapshot.data}원',
            style: TextStyle(fontSize: 24),
          );
        } else {
          return CircularProgressIndicator();
        }
      },
    );
  }
}
```

### 문제 13: 키보드 오버레이 버그
```dart
class RegistrationForm extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('회원 가입')),
      body: Column(
        children: [
          TextField(
            decoration: InputDecoration(labelText: '이름'),
          ),
          TextField(
            decoration: InputDecoration(labelText: '이메일'),
          ),
          TextField(
            decoration: InputDecoration(labelText: '비밀번호'),
            obscureText: true,
          ),
          Spacer(),
          ElevatedButton(
            onPressed: () {},
            child: Text('가입하기'),
          ),
        ],
      ),
    );
  }
}
```

### 문제 14: 비동기 초기화 버그
```dart
class UserProfileWidget extends StatefulWidget {
  @override
  _UserProfileWidgetState createState() => _UserProfileWidgetState();
}

class _UserProfileWidgetState extends State<UserProfileWidget> {
  Map<String, dynamic> userData = {};
  bool isLoading = false;
  
  void fetchUserData() async {
    isLoading = true;
    // API 호출을 시뮬레이션
    await Future.delayed(Duration(seconds: 2));
    userData = {
      'name': '김철수',
      'email': 'user@example.com',
      'points': 1250
    };
    isLoading = false;
  }
  
  @override
  void initState() {
    super.initState();
    fetchUserData();
  }
  
  @override
  Widget build(BuildContext context) {
    return Card(
      child: isLoading
          ? Center(child: CircularProgressIndicator())
          : ListTile(
              title: Text(userData['name']),
              subtitle: Text(userData['email']),
              trailing: Text('${userData['points']} 포인트'),
            ),
    );
  }
}
```

### 문제 15: 위젯 트리 재구성 버그
```dart
class DynamicTabsWidget extends StatefulWidget {
  @override
  _DynamicTabsWidgetState createState() => _DynamicTabsWidgetState();
}

class _DynamicTabsWidgetState extends State<DynamicTabsWidget> with SingleTickerProviderStateMixin {
  late TabController _tabController;
  List<String> _tabs = ['Tab 1', 'Tab 2'];
  
  @override
  void initState() {
    super.initState();
    _tabController = TabController(length: _tabs.length, vsync: this);
  }
  
  void _addNewTab() {
    setState(() {
      _tabs.add('Tab ${_tabs.length + 1}');
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('동적 탭'),
        bottom: TabBar(
          controller: _tabController,
          tabs: _tabs.map((tab) => Tab(text: tab)).toList(),
        ),
        actions: [
          IconButton(
            icon: Icon(Icons.add),
            onPressed: _addNewTab,
          ),
        ],
      ),
      body: TabBarView(
        controller: _tabController,
        children: _tabs.map((tab) => Center(child: Text(tab))).toList(),
      ),
    );
  }
  
  @override
  void dispose() {
    _tabController.dispose();
    super.dispose();
  }
}
```

### 문제 16: Provider 사용 버그
```dart
// main.dart
void main() {
  runApp(
    ChangeNotifierProvider(
      create: (context) => CartModel(),
      child: const MyApp(),
    ),
  );
}

// cart_model.dart
class CartModel extends ChangeNotifier {
  final List<String> _items = [];
  
  List<String> get items => _items;
  
  void addItem(String item) {
    _items.add(item);
    notifyListeners();
  }
  
  void removeItem(String item) {
    _items.remove(item);
    notifyListeners();
  }
}

// product_screen.dart
class ProductScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('상품 목록')),
      body: ListView(
        children: [
          ProductItem('상품 1'),
          ProductItem('상품 2'),
          ProductItem('상품 3'),
        ],
      ),
    );
  }
}

// product_item.dart
class ProductItem extends StatelessWidget {
  final String name;
  
  const ProductItem(this.name, {Key? key}) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    var cart = Provider.of<CartModel>(context);
    
    return ListTile(
      title: Text(name),
      trailing: IconButton(
        icon: Icon(Icons.add_shopping_cart),
        onPressed: () {
          cart.addItem(name);
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(content: Text('$name 추가됨')),
          );
        },
      ),
    );
  }
}
```

### 문제 17: 카운터 앱 버그
```dart
class CounterProvider extends ChangeNotifier {
  int _count = 0;
  int get count => _count;
  
  void increment() {
    _count++;
  }
}

class CounterScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ChangeNotifierProvider(
      create: (context) => CounterProvider(),
      child: Scaffold(
        appBar: AppBar(title: Text('카운터 앱')),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              Text('현재 카운트:'),
              Consumer<CounterProvider>(
                builder: (context, counter, child) {
                  return Text(
                    '${counter.count}',
                    style: TextStyle(fontSize: 40),
                  );
                },
              ),
            ],
          ),
        ),
        floatingActionButton: Consumer<CounterProvider>(
          builder: (context, counter, child) {
            return FloatingActionButton(
              onPressed: counter.increment,
              child: Icon(Icons.add),
            );
          },
        ),
      ),
    );
  }
}
```

### 문제 18: TodoList 앱 버그
```dart
class TodoListProvider extends ChangeNotifier {
  List<String> _todos = ['할 일 1', '할 일 2', '할 일 3'];
  
  List<String> get todos => _todos;
  
  void addTodo(String todo) {
    todos.add(todo);
    notifyListeners();
  }
  
  void removeTodo(int index) {
    todos.removeAt(index);
    notifyListeners();
  }
}

class TodoListScreen extends StatelessWidget {
  final TextEditingController _controller = TextEditingController();
  
  @override
  Widget build(BuildContext context) {
    return ChangeNotifierProvider(
      create: (context) => TodoListProvider(),
      child: Scaffold(
        appBar: AppBar(title: Text('할 일 목록')),
        body: Column(
          children: [
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: Row(
                children: [
                  Expanded(
                    child: TextField(
                      controller: _controller,
                      decoration: InputDecoration(
                        labelText: '새 할 일',
                      ),
                    ),
                  ),
                  Consumer<TodoListProvider>(
                    builder: (context, provider, child) {
                      return IconButton(
                        icon: Icon(Icons.add),
                        onPressed: () {
                          if (_controller.text.isNotEmpty) {
                            provider.addTodo(_controller.text);
                            _controller.clear();
                          }
                        },
                      );
                    },
                  ),
                ],
              ),
            ),
            Expanded(
              child: Consumer<TodoListProvider>(
                builder: (context, provider, child) {
                  return ListView.builder(
                    itemCount: provider.todos.length,
                    itemBuilder: (context, index) {
                      return ListTile(
                        title: Text(provider.todos[index]),
                        trailing: IconButton(
                          icon: Icon(Icons.delete),
                          onPressed: () => provider.removeTodo(index),
                        ),
                      );
                    },
                  );
                },
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

### 문제 19: 여러 Provider 사용 버그
```dart
class UserProvider extends ChangeNotifier {
  String _name = '게스트';
  String get name => _name;
  
  void setName(String name) {
    _name = name;
    notifyListeners();
  }
}

class ThemeProvider extends ChangeNotifier {
  bool _isDarkMode = false;
  bool get isDarkMode => _isDarkMode;
  
  void toggleTheme() {
    _isDarkMode = !_isDarkMode;
    notifyListeners();
  }
}

class SettingsScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final userProvider = Provider.of<UserProvider>(context);
    
    return Scaffold(
      appBar: AppBar(title: Text('설정')),
      body: Column(
        children: [
          ListTile(
            title: Text('사용자 이름'),
            subtitle: Text(userProvider.name),
            trailing: IconButton(
              icon: Icon(Icons.edit),
              onPressed: () {
                // 사용자 이름 변경 다이얼로그
              },
            ),
          ),
          SwitchListTile(
            title: Text('다크 모드'),
            value: Provider.of<ThemeProvider>(context).isDarkMode,
            onChanged: (value) {
              Provider.of<ThemeProvider>(context, listen: false).toggleTheme();
            },
          ),
        ],
      ),
    );
  }
}

// main.dart
void main() {
  runApp(
    MultiProvider(
      providers: [
        ChangeNotifierProvider(create: (context) => UserProvider()),
      ],
      child: const MyApp(),
    ),
  );
}
```

### 문제 20: 로그인 프로세스 버그
```dart
class AuthProvider extends ChangeNotifier {
  bool _isLoggedIn = false;
  String _username = '';
  
  bool get isLoggedIn => _isLoggedIn;
  String get username => _username;
  
  Future<bool> login(String username, String password) async {
    // 로그인 API 호출 시뮬레이션
    await Future.delayed(Duration(seconds: 1));
    
    if (username == 'admin' && password == 'password') {
      _isLoggedIn = true;
      _username = username;
      notifyListeners();
      return true;
    }
    return false;
  }
  
  void logout() {
    _isLoggedIn = false;
    _username = '';
    notifyListeners();
  }
}

class LoginScreen extends StatelessWidget {
  final TextEditingController _usernameController = TextEditingController();
  final TextEditingController _passwordController = TextEditingController();
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('로그인')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            TextField(
              controller: _usernameController,
              decoration: InputDecoration(labelText: '사용자 이름'),
            ),
            TextField(
              controller: _passwordController,
              decoration: InputDecoration(labelText: '비밀번호'),
              obscureText: true,
            ),
            SizedBox(height: 20),
            Consumer<AuthProvider>(
              builder: (context, auth, child) {
                return ElevatedButton(
                  onPressed: () async {
                    final success = await auth.login(
                      _usernameController.text,
                      _passwordController.text,
                    );
                    
                    if (success) {
                      Navigator.pushReplacement(
                        context,
                        MaterialPageRoute(
                          builder: (context) => HomeScreen(),
                        ),
                      );
                    } else {
                      ScaffoldMessenger.of(context).showSnackBar(
                        SnackBar(content: Text('로그인 실패')),
                      );
                    }
                  },
                  child: Text('로그인'),
                );
              },
            ),
          ],
        ),
      ),
    );
  }
}
```

### 문제 21: Stacked Architecture 버그
```dart
// services/api_service.dart
class ApiService {
  Future<List<WorkoutData>> getWorkouts() async {
    // API 호출 시뮬레이션
    await Future.delayed(Duration(seconds: 1));
    return [
      WorkoutData(id: '1', name: '러닝', calories: 350),
      WorkoutData(id: '2', name: '웨이트 트레이닝', calories: 280),
      WorkoutData(id: '3', name: '요가', calories: 180),
    ];
  }
}

// models/workout_data.dart
class WorkoutData {
  final String id;
  final String name;
  final int calories;

  WorkoutData({required this.id, required this.name, required this.calories});
}

// viewmodels/workouts_viewmodel.dart
class WorkoutsViewModel extends BaseViewModel {
  final ApiService _apiService;
  List<WorkoutData> workouts = [];
  
  WorkoutsViewModel({required ApiService apiService}) : _apiService = apiService;
  
  Future fetchWorkouts() async {
    setBusy(true);
    workouts = await _apiService.getWorkouts();
    setBusy(false);
  }
}

// views/workouts_view.dart
class WorkoutsView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ViewModelBuilder<WorkoutsViewModel>.reactive(
      viewModelBuilder: () => WorkoutsViewModel(
        apiService: ApiService(),
      ),
      onModelReady: (model) => model.fetchWorkouts(),
      builder: (context, model, child) {
        return Scaffold(
          appBar: AppBar(title: Text('운동 목록')),
          body: model.isBusy
              ? Center(child: CircularProgressIndicator())
              : ListView.builder(
                  itemCount: model.workouts.length,
                  itemBuilder: (context, index) {
                    final workout = model.workouts[index];
                    return ListTile(
                      title: Text(workout.name),
                      subtitle: Text('${workout.calories} 칼로리'),
                      onTap: () {
                        Navigator.push(
                          context,
                          MaterialPageRoute(
                            builder: (context) => WorkoutDetailView(workout: workout),
                          ),
                        );
                      },
                    );
                  },
                ),
        );
      },
    );
  }
}
```

### 문제 22: GraphQL with Ferry 버그
```dart
// graphql/workout_queries.dart
const String getWorkoutsQuery = r'''
  query GetWorkouts {
    workouts {
      id
      name
      calories
    }
  }
''';

// services/graphql_client.dart
Client initClient() {
  final cache = Cache(store: MemoryStore());
  
  final link = HttpLink('https://api.fitness-app.com/graphql');
  
  final client = Client(
    link: link,
    cache: cache,
  );
  
  return client;
}

// viewmodels/workouts_viewmodel.dart
class WorkoutsViewModel extends BaseViewModel {
  final Client _client;
  List<Workout> workouts = [];
  
  WorkoutsViewModel({required Client client}) : _client = client;
  
  Future fetchWorkouts() async {
    setBusy(true);
    
    final operation = Operation(
      document: gql(getWorkoutsQuery),
    );
    
    final response = await _client.request(operation).first;
    
    if (response.data != null) {
      final workoutsData = response.data!['workouts'] as List;
      workouts = workoutsData.map((w) => Workout.fromJson(w)).toList();
    }
    
    setBusy(false);
  }
}
```

### 문제 23: BLE 연결 버그
```dart
// services/ble_service.dart
class BleService {
  FlutterBluePlus flutterBlue = FlutterBluePlus.instance;
  BluetoothDevice? device;
  BluetoothCharacteristic? dataCharacteristic;
  
  Stream<List<int>> get dataStream => dataCharacteristic!.value;
  
  Future<bool> connectToDevice(String deviceId) async {
    try {
      final devices = await flutterBlue.scanForDevices(timeout: Duration(seconds: 5));
      
      final targetDevice = devices.firstWhere(
        (d) => d.id.toString() == deviceId,
        orElse: () => throw Exception('Device not found'),
      );
      
      await targetDevice.connect();
      device = targetDevice;
      
      final services = await device!.discoverServices();
      final service = services.firstWhere(
        (s) => s.uuid.toString() == '0000180d-0000-1000-8000-00805f9b34fb',
      );
      
      dataCharacteristic = service.characteristics.firstWhere(
        (c) => c.uuid.toString() == '00002a37-0000-1000-8000-00805f9b34fb',
      );
      
      await dataCharacteristic!.setNotifyValue(true);
      
      return true;
    } catch (e) {
      print('BLE connection error: $e');
      return false;
    }
  }
  
  void disconnect() {
    if (device != null) {
      device!.disconnect();
      device = null;
      dataCharacteristic = null;
    }
  }
}

// viewmodels/heart_rate_viewmodel.dart
class HeartRateViewModel extends BaseViewModel {
  final BleService _bleService;
  int currentHeartRate = 0;
  StreamSubscription? _dataSubscription;
  
  HeartRateViewModel({required BleService bleService}) : _bleService = bleService;
  
  Future connectToDevice(String deviceId) async {
    setBusy(true);
    final success = await _bleService.connectToDevice(deviceId);
    
    if (success) {
      _dataSubscription = _bleService.dataStream.listen((data) {
        // 심박수 데이터 파싱
        if (data.isNotEmpty) {
          currentHeartRate = data[1];
          notifyListeners();
        }
      });
    }
    
    setBusy(false);
    return success;
  }
  
  void dispose() {
    _dataSubscription?.cancel();
    _bleService.disconnect();
    super.dispose();
  }
}
```

### 문제 24: 비동기 처리 버그
```dart
// services/workout_service.dart
class WorkoutService {
  Future<List<Exercise>> getExercises() async {
    // 서버로부터 운동 목록 가져오기
    await Future.delayed(Duration(seconds: 1));
    return [
      Exercise(id: '1', name: '푸시업', muscleGroup: '가슴'),
      Exercise(id: '2', name: '스쿼트', muscleGroup: '하체'),
      Exercise(id: '3', name: '풀업', muscleGroup: '등'),
    ];
  }
  
  Future<WorkoutPlan> createWorkoutPlan(List<String> exerciseIds) async {
    // 운동 계획 생성하기
    await Future.delayed(Duration(seconds: 2));
    return WorkoutPlan(
      id: 'plan-${DateTime.now().millisecondsSinceEpoch}',
      name: '커스텀 플랜',
      exercises: exerciseIds,
    );
  }
}

// viewmodels/plan_creator_viewmodel.dart
class PlanCreatorViewModel extends BaseViewModel {
  final WorkoutService _workoutService;
  List<Exercise> availableExercises = [];
  List<String> selectedExerciseIds = [];
  WorkoutPlan? createdPlan;
  
  PlanCreatorViewModel({required WorkoutService workoutService})
      : _workoutService = workoutService;
  
  Future initialize() async {
    setBusy(true);
    availableExercises = await _workoutService.getExercises();
    setBusy(false);
  }
  
  void toggleExercise(String exerciseId) {
    if (selectedExerciseIds.contains(exerciseId)) {
      selectedExerciseIds.remove(exerciseId);
    } else {
      selectedExerciseIds.add(exerciseId);
    }
    notifyListeners();
  }
  
  Future createPlan() async {
    if (selectedExerciseIds.isEmpty) {
      return;
    }
    
    setBusy(true);
    createdPlan = await _workoutService.createWorkoutPlan(selectedExerciseIds);
    setBusy(false);
    
    // 성공 메시지 또는 네비게이션
  }
}

// views/plan_creator_view.dart
class PlanCreatorView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ViewModelBuilder<PlanCreatorViewModel>.reactive(
      viewModelBuilder: () => PlanCreatorViewModel(
        workoutService: WorkoutService(),
      ),
      onModelReady: (model) => model.initialize(),
      builder: (context, model, child) {
        return Scaffold(
          appBar: AppBar(title: Text('운동 계획 생성')),
          body: model.isBusy
              ? Center(child: CircularProgressIndicator())
              : ListView.builder(
                  itemCount: model.availableExercises.length,
                  itemBuilder: (context, index) {
                    final exercise = model.availableExercises[index];
                    final isSelected = model.selectedExerciseIds.contains(exercise.id);
                    
                    return CheckboxListTile(
                      title: Text(exercise.name),
                      subtitle: Text(exercise.muscleGroup),
                      value: isSelected,
                      onChanged: (_) => model.toggleExercise(exercise.id),
                    );
                  },
                ),
          floatingActionButton: FloatingActionButton(
            onPressed: () async {
              await model.createPlan();
              if (model.createdPlan != null) {
                Navigator.pushReplacement(
                  context,
                  MaterialPageRoute(
                    builder: (context) => PlanDetailView(plan: model.createdPlan!),
                  ),
                );
              }
            },
            child: Icon(Icons.check),
          ),
        );
      },
    );
  }
}
```

### 문제 25: fl_chart 데이터 시각화 버그
```dart
// viewmodels/workout_stats_viewmodel.dart
class WorkoutStatsViewModel extends BaseViewModel {
  final ApiService _apiService;
  List<WorkoutDay> weeklyData = [];
  
  WorkoutStatsViewModel({required ApiService apiService}) : _apiService = apiService;
  
  Future fetchWeeklyData() async {
    setBusy(true);
    try {
      weeklyData = await _apiService.getWeeklyWorkouts();
    } catch (e) {
      // 에러 처리
    }
    setBusy(false);
  }
  
  List<FlSpot> getCalorieSpots() {
    final spots = <FlSpot>[];
    for (int i = 0; i < weeklyData.length; i++) {
      spots.add(FlSpot(i.toDouble(), weeklyData[i].calories.toDouble()));
    }
    return spots;
  }
}

// views/workout_stats_view.dart
class WorkoutStatsView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ViewModelBuilder<WorkoutStatsViewModel>.reactive(
      viewModelBuilder: () => WorkoutStatsViewModel(
        apiService: ApiService(),
      ),
      onModelReady: (model) => model.fetchWeeklyData(),
      builder: (context, model, child) {
        if (model.isBusy) {
          return Center(child: CircularProgressIndicator());
        }
        
        return Scaffold(
          appBar: AppBar(title: Text('운동 통계')),
          body: Padding(
            padding: const EdgeInsets.all(16.0),
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text(
                  '주간 칼로리 소모량',
                  style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                ),
                SizedBox(height: 16),
                Container(
                  height: 300,
                  child: LineChart(
                    LineChartData(
                      gridData: FlGridData(show: true),
                      titlesData: FlTitlesData(
                        leftTitles: SideTitles(showTitles: true),
                        bottomTitles: SideTitles(
                          showTitles: true,
                          getTitles: (value) {
                            final weekdays = ['월', '화', '수', '목', '금', '토', '일'];
                            final index = value.toInt();
                            if (index >= 0 && index < weekdays.length) {
                              return weekdays[index];
                            }
                            return '';
                          },
                        ),
                      ),
                      lineBarsData: [
                        LineChartBarData(
                          spots: model.getCalorieSpots(),
                          isCurved: true,
                          colors: [Colors.blue],
                          barWidth: 4,
                          belowBarData: BarAreaData(show: true),
                        ),
                      ],
                    ),
                  ),
                ),
              ],
            ),
          ),
        );
      },
    );
  }
}
```
