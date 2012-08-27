---
layout: post
title: "Makefile 예제"
description: ""
category: Linux
tags: [Makefile, Linux]
---
{% include JB/setup %}

## 예제1. Application

	CXX=g++
	CFLAGS=-c -Wall
	LDFLAGS=
	SOURCES=main.cpp
	OBJECTS=$(SOURCES:.cpp=.o)
	EXECUTABLE=a.out

	all: $(SOURCES) $(EXECUTABLE)

	$(EXECUTABLE): $(OBJECTS)
		$(CXX) $(LDFLAGS) $(OBJECTS) -o $@

	.cpp.o:
		$(CXX) $(CFLAGS) $< -o $@

	clean:
		rm -f $(OBJECTS)



## 예제2. Application - C와 CPP 복합

	CC=gcc
	CXX=g++

	CFLAGS=-c -Wall
	LDFLAGS=-lpthread

	SOURCES=main.cpp VoiceSound2.cpp
	OBJECTS=$(SOURCES:.cpp=.o)

	SOURCES_C=sound/mixer.c sound/player.c
	OBJECTS_C=$(SOURCES_C:.c=.o)

	EXECUTABLE=test

	all: $(SOURCES) $(EXECUTABLE)

	$(EXECUTABLE): $(OBJECTS) $(OBJECTS_C)
		$(CXX) $(LDFLAGS) $(OBJECTS) $(OBJECTS_C) -o $@

	.cpp.o:
		$(CXX) $(CFLAGS) $< -o $@
		
	.c.o:
		$(CC) $(CFLAGS) $< -o $@

	clean:
		rm -f $(OBJECTS) $(OBJECTS_C)



## 예제3. static library

	CC=g++
	CFLAGS=-c -Wall
	SOURCES=GPSReader.cpp
	OBJECTS=$(SOURCES:.cpp=.o)
	TARGET=libnd100s

	all: $(SOURCES) $(TARGET)

	$(TARGET): $(OBJECTS)
		ar cr $(TARGET).a $(OBJECTS)

	.cpp.o:
		$(CC) $(CFLAGS) $< -o $@

	clean:
		rm -f $(OBJECTS) $(TARGET).a



