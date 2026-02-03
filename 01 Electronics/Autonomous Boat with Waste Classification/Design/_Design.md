![[Pasted image 20260125124535.png]]

| Pins Used | Purpose |     |
| --------- | ------- | --- |
| **16**    | OK      | OK  |
| **17**    | OK      | OK  |
| **18**    | OK      | OK  |
| **19**    | OK      | OK  |



```cpp
  // Also keep serial control working
  if (Serial.available()) {
	    char command = Serial.read();
	    switch (command) {
	    case 'F':
	    case 'f':
	      moveForward(255);
	      break;
	    case 'B':
	    case 'b':
	      moveBackward(255);
	      break;
	    case 'L':
	    case 'l':
	      turnLeft(255);
	      break;
	    case 'R':
	    case 'r':
	      turnRight(255);
	      break;
	    case 'S':
	    case 's':
	      stopMotors();
	      break;
	    }
  }
```

