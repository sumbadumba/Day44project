# Day44project

프로젝트짤때 띄어쓰기와 주석 열심히 달기





/*
# 숙제1
XML을 읽어들여 화면에 출력하는 프로그램을 만들어보자
	 - XML의 내용을 최대한 그대로 출력
	
*/FindAttribute를 이용할 것



http://blog.naver.com/PostView.nhn?blogId=chansung0602&logNo=221014997196&categoryNo=0&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1&from=postView





.cpp


#include "framework.h"
#include "ExecuteNotes.h"
#include "Objects/GameObject.h"

ExecuteNotes::ExecuteNotes(ExecuteValues* values)
	: Execute(values)
{

	// 로딩

	D2D::GetDesc(&desc);
	hdc = GetDC(desc.Handle);
	ReadXML(L"twinkleXML.xml");

	int i = 0;
	while (!events.empty())
	{
		NoteEvent event = events.front();
		wstring str = L"<Note time=\"" + to_wstring(event.time) + L"\" index=\"" + to_wstring(event.index) + L"\" />\n";
		xmlStr[i] = str;
		events.pop();
		i++;
	}
}

ExecuteNotes::~ExecuteNotes()
{

	DiscardDeviceResources();
}

void ExecuteNotes::DiscardDeviceResources()
{
}

void ExecuteNotes::Update()
{ 

}

void ExecuteNotes::Render()
{
	wstring str = L"<?xml version=\"1.0\" encoding=\"UTF - 8\" />";
	TextOut(hdc, 80.0f, 10.0f, str.c_str(),str.length());

	str = L"<Melody>";
	TextOut(hdc, 80.0f, 30.0f, str.c_str(),str.length());

	for(int i=0; i<42; i++)
		TextOut(hdc, 100.0f, 50.0f + i * 20.0f, xmlStr[i].c_str(), xmlStr[i].length());

	str = L"</Melody>";
	TextOut(hdc, 80.0f, 50.0f + 42 * 20.0f, str.c_str(), str.length());

}
void ExecuteNotes::ResizeScreen()
{
}

void ExecuteNotes::ReadXML(wstring file)
{
	Xml::XMLDocument* document = new Xml::XMLDocument();

	wstring tempFile = file;
	Xml::XMLError error = document->LoadFile(String::ToString(tempFile).c_str());
	assert(error == Xml::XML_SUCCESS);

	Xml::XMLElement* root = document->FirstChildElement();
	Xml::XMLElement* noteNode = root->FirstChildElement();

	do 
	{
		NoteEvent noteEvent;

		noteEvent.time = noteNode->FloatAttribute("time");
		noteEvent.index = noteNode->IntAttribute("index");
		//noteNode->GetText();
		events.push(noteEvent);
		//
		noteNode = noteNode->NextSiblingElement();
	} while (noteNode != NULL);

	SAFE_DELETE(document);
}

void ExecuteNotes::print(int i, wstring str)
{

}





.h
#pragma once
#include "Execute.h"

#define DROP_OBJECT_MAX 5	// 하나의 음에 해당하는 캐시 개수
#define DROP_OBJECT_TYPE_COUNT 8	// 도~도(옥타브) 까지 흰건반 개수
#define DROP_IMAGE_HEIGHT 128		// 정해진 이미지 파일의 높이


class ExecuteNotes : public Execute
{
private:
	struct NoteObject
	{
		class GameObject * object;	// 이동에 사용될 게임오브젝트
		int index;	// 원래 어느음에 해당하는지 생성될 때 인덱스
	};

	struct NoteEvent
	{
		float time;
		int index;
	};
public:
	ExecuteNotes(ExecuteValues* values);
	~ExecuteNotes();

	void DiscardDeviceResources() override;
	void Update() override;
	void Render() override;
	void ResizeScreen() override;

private:
	void ReadXML(wstring file);

	void print(int i, wstring str);


private:
	queue<NoteObject> DropNotes[DROP_OBJECT_TYPE_COUNT];	// 큐가 DROP_OBJECT_TYPE_COUNT 개수만큼 존재
	list<NoteObject> usingNotes;	// 사용중인 오브젝트 저장소

	queue<NoteEvent> events;
	float startTime;

	GameObject * KeyboardObjects[DROP_OBJECT_TYPE_COUNT];
	wstring xmlStr[42];
	HDC hdc;
	D2DDesc desc;
};

