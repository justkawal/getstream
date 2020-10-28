# GetStream

GetStream is the lightest and most performative way of working with events at Dart. Streams are cool, but you don't always need them. ChangeNotifier is easy, but not as powerful, is slow and makes it difficult to remove listeners. GetStream is very light, and works with simple callbacks. In this way, every event calls only one function. There is no buffering, and you have very low memory consumption.

## Usage

A simple usage example:

```dart
import 'package:getstream/getstream.dart';

void main() {
  final controller = GetStream<int>();
  controller.listen((event) {
    print('change number to $event');
  });
  controller.add(2);
  controller.add(3);
  controller.add(4);
 
  final subs = controller.listen((event) {
    print('change number to $event');
  });
  controller.add(5);
  controller.add(6);
  controller.add(2);

  subs.cancel();
  controller.add(1);

  controller.close();
}


```
## How more fast? 
In tests on the VM dart, GetStream is 2650% faster than the conventional Stream. If you want to run this test yourself, you can run this sample code:

```dart
import 'dart:async';
import 'package:test/test.dart';
import 'package:getstream/getstream.dart';

void main() async {
  test('run benchmarks', () async {
    print(await stream());
    print(await getStream());
  });
}

int times = 30000;
int get last => times - 1;

Future<String> getStream() {
  final c = Completer<String>();
  final value = GetStream<int>();
  final timer = Stopwatch();
  timer.start();

  value.listen((v) {
    if (last == v) {
      timer.stop();
      c.complete(
          '''$v listeners notified | [GET_STREAM] objs time: ${timer.elapsedMicroseconds}ms''');
    }
  });

  for (var i = 0; i < times; i++) {
    value.add(i);
  }

  return c.future;
}

Future<String> stream() {
  final c = Completer<String>();
  final value = StreamController<int>();
  final timer = Stopwatch();
  timer.start();

  value.stream.listen((v) {
    if (last == v) {
      timer.stop();
      c.complete(
          '''$v listeners notified | [STREAM] objs time: ${timer.elapsedMicroseconds}ms''');
    }
  });

  for (var i = 0; i < times; i++) {
    value.add(i);
  }

  return c.future;
}

```
