# NotePad
This is an AndroidStudio rebuild of google SDK sample NotePad
# 新增类,修改类（部分代码展示）

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Newclass.jpg)

• MyCursorAdapter.java
public void bindView(View view, Context context, Cursor cursor){
        super.bindView(view, context, cursor);
        //从数据库中读取的cursor中获取笔记列表对应的颜色数据，并设置笔记颜色
        int x = cursor.getInt(cursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_BACK_COLOR));

        switch (x){
            case NotePad.Notes.DEFAULT_COLOR:
                view.setBackgroundColor(Color.rgb(255, 255, 255));
                break;
            case NotePad.Notes.YELLOW_COLOR:
                view.setBackgroundColor(Color.rgb(247, 216, 133));
                break;
            case NotePad.Notes.BLUE_COLOR:
                view.setBackgroundColor(Color.rgb(165, 202, 237));
                break;
            case NotePad.Notes.GREEN_COLOR:
                view.setBackgroundColor(Color.rgb(161, 214, 174));
                break;
            case NotePad.Notes.RED_COLOR:
                view.setBackgroundColor(Color.rgb(244, 149, 133));
                break;
            default:
                view.setBackgroundColor(Color.rgb(255, 255, 255));
                break;
        }
    }

• NoteColor.java
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.note_color);
    mUri = getIntent().getData();
    mCursor = managedQuery(
            mUri,        // The URI for the note that is to be retrieved.
            PROJECTION,  // The columns to retrieve
            null,        // No selection criteria are used, so no where columns are needed.
            null,        // No where columns are used, so no where values are needed.
            null         // No sort order is needed.
        );
}

• NoteSearch.java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.note_search_list);
        Intent intent = getIntent();
        if (intent.getData() == null) {
            intent.setData(NotePad.Notes.CONTENT_URI);
        }
        SearchView searchview = (SearchView)findViewById(R.id.search_view);
        searchview.setOnQueryTextListener(NoteSearch.this);  //为查询文本框注册监听器
    }
    
• OutputText.java
private void write()
    {
        try
        {
            // 如果手机插入了SD卡，而且应用程序具有访问SD的权限
            if (Environment.getExternalStorageState().equals(
                    Environment.MEDIA_MOUNTED)) {
                // 获取SD卡的目录
                File sdCardDir = Environment.getExternalStorageDirectory();
                //创建文件目录
                File targetFile = new File(sdCardDir.getCanonicalPath() + "/" + mName.getText() + ".txt");
                //写文件
                PrintWriter ps = new PrintWriter(new OutputStreamWriter(new FileOutputStream(targetFile), "UTF-8"));
                ps.println(TITLE);
                ps.println(NOTE);
                ps.println("创建时间：" + CREATE_DATE);
                ps.println("最后一次修改时间：" + MODIFICATION_DATE);
                ps.close();
                Toast.makeText(this, "保存成功,保存位置：" + sdCardDir.getCanonicalPath() + "/" + mName.getText() + ".txt", Toast.LENGTH_LONG).show();
            }
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
    }
    
• NoteEditor.java
    设置颜色
    switch (x){
                case NotePad.Notes.DEFAULT_COLOR:
                    mText.setBackgroundColor(Color.rgb(255, 255, 255));
                    break;
                case NotePad.Notes.YELLOW_COLOR:
                    mText.setBackgroundColor(Color.rgb(247, 216, 133));
                    break;
                case NotePad.Notes.BLUE_COLOR:
                    mText.setBackgroundColor(Color.rgb(165, 202, 237));
                    break;
                case NotePad.Notes.GREEN_COLOR:
                    mText.setBackgroundColor(Color.rgb(161, 214, 174));
                    break;
                case NotePad.Notes.RED_COLOR:
                    mText.setBackgroundColor(Color.rgb(244, 149, 133));
                    break;
                default:
                    mText.setBackgroundColor(Color.rgb(255, 255, 255));
                    break;
            }
            
• NotePad.java
    设置颜色
    public static final int DEFAULT_COLOR = 0; //白
        public static final int YELLOW_COLOR = 1;//黄
        public static final int BLUE_COLOR = 2;//蓝
        public static final int GREEN_COLOR = 3;//绿
        public static final int RED_COLOR = 4;//红
        
• NotesList.java
    设置搜索，排序
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.menu_add:
          /*
           * Launches a new Activity using an Intent. The intent filter for the Activity
           * has to have action ACTION_INSERT. No category is set, so DEFAULT is assumed.
           * In effect, this starts the NoteEditor Activity in NotePad.
           */
                startActivity(new Intent(Intent.ACTION_INSERT, getIntent().getData()));
                return true;
            case R.id.menu_paste:
          /*
           * Launches a new Activity using an Intent. The intent filter for the Activity
           * has to have action ACTION_PASTE. No category is set, so DEFAULT is assumed.
           * In effect, this starts the NoteEditor Activity in NotePad.
           */
                startActivity(new Intent(Intent.ACTION_PASTE, getIntent().getData()));
                return true;

            //添加搜素
            case R.id.menu_search:
                Intent intent = new Intent();
                intent.setClass(NotesList.this,NoteSearch.class);
                NotesList.this.startActivity(intent);
                return true;

            //创建时间排序
            case R.id.menu_sort1:
                cursor = managedQuery(
                        getIntent().getData(),            // Use the default content URI for the provider.
                        PROJECTION,                       // Return the note ID and title for each note. and modifcation date
                        null,                             // No where clause, return all records.
                        null,                             // No where clause, therefore no where column values.
                        NotePad.Notes._ID  // Use the default sort order.
                );
                adapter = new MyCursorAdapter(
                        this,
                        R.layout.noteslist_item,
                        cursor,
                        dataColumns,
                        viewIDs
                );
                setListAdapter(adapter);
                return true;

            //修改时间排序
            case R.id.menu_sort2:
                cursor = managedQuery(
                        getIntent().getData(),            // Use the default content URI for the provider.
                        PROJECTION,                       // Return the note ID and title for each note. and modifcation date
                        null,                             // No where clause, return all records.
                        null,                             // No where clause, therefore no where column values.
                        NotePad.Notes.DEFAULT_SORT_ORDER // Use the default sort order.
                );

                adapter = new MyCursorAdapter(
                        this,
                        R.layout.noteslist_item,
                        cursor,
                        dataColumns,
                        viewIDs
                );
                setListAdapter(adapter);
                return true;

            //颜色排序
            case R.id.menu_sort3:
                cursor = managedQuery(
                        getIntent().getData(),            // Use the default content URI for the provider.
                        PROJECTION,                       // Return the note ID and title for each note. and modifcation date
                        null,                             // No where clause, return all records.
                        null,                             // No where clause, therefore no where column values.
                        NotePad.Notes.COLUMN_NAME_BACK_COLOR // Use the default sort order.
                );
                adapter = new MyCursorAdapter(
                        this,
                        R.layout.noteslist_item,
                        cursor,
                        dataColumns,
                        viewIDs
                );
                setListAdapter(adapter);
                return true;

            default:
                return super.onOptionsItemSelected(item);
        }
    }


# 新建布局，修改布局

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Newlayout.jpg)

• note_color.xml
  举例
  <ImageButton
        android:id="@+id/color_white"
        android:layout_width="0dp"
        android:layout_height="50dp"
        android:layout_weight="1"
        android:background="@color/colorWhite"
        android:onClick="white"/>

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Colorlayout.jpg)

• note_srarch_list.xml
  <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageButton
        android:id="@+id/color_white"
        android:layout_width="0dp"
        android:layout_height="50dp"
        android:layout_weight="1"
        android:background="@color/colorWhite"
        android:onClick="white"/>

    <ImageButton
        android:id="@+id/color_yellow"
        android:layout_width="0dp"
        android:layout_height="50dp"
        android:layout_weight="1"
        android:background="@color/colorYellow"
        android:onClick="yellow"/>

    <ImageButton
        android:id="@+id/color_blue"
        android:layout_width="0dp"
        android:layout_height="50dp"
        android:layout_weight="1"
        android:background="@color/colorBlue"
        android:onClick="blue"/>

    <ImageButton
        android:id="@+id/color_green"
        android:layout_width="0dp"
        android:layout_height="50dp"
        android:layout_weight="1"
        android:background="@color/colorGreen"
        android:onClick="green"/>

    <ImageButton
        android:id="@+id/color_red"
        android:layout_width="0dp"
        android:layout_height="50dp"
        android:layout_weight="1"
        android:background="@color/colorRed"
        android:onClick="red"/>
    
</LinearLayout>

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Searchlayout.jpg)
        
• output_text.xml
  <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:paddingLeft="6dip"
    android:paddingRight="6dip"
    android:paddingBottom="3dip">

    <EditText android:id="@+id/output_name"
        android:maxLines="1"
        android:layout_marginTop="2dp"
        android:layout_marginBottom="15dp"
        android:layout_width="wrap_content"
        android:ems="25"
        android:layout_height="wrap_content"
        android:autoText="true"
        android:capitalize="sentences"
        android:scrollHorizontally="true" />

    <Button android:id="@+id/output_ok"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="right"
        android:text="@string/output_ok"
        android:onClick="OutputOk" />

</LinearLayout>

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Outputlayout.jpg)        

• colors.xml

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Settingcolor.jpg)
 
# 页面布局
![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Pagelayout.jpg)

# 基本要求：
• NoteList中显示条目增加时间戳显示

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Timestamp.jpg)

• 添加笔记查询功能（根据标题查询）
默认title为输入内容，可自行更改，但查询是按照title查询，若title与笔记内容则笔记内容便无法查询到

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Notequery1.jpg)

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Notequery2.jpg)

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Notequery3.jpg)

# 附加功能：
• UI美化
我的排版参考PPT中的UI美化，将功能按键安排在上方

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/UIbeautification.jpg)

• 更改记事本的背景
需要在笔记中设置，在笔记初始展示页以带背景颜色的横条表示

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Backgroundreplacement1.jpg)

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Backgroundreplacement2.jpg)

• 导出笔记

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Exportnotes1.jpg)

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Exportnotes2.jpg)

• 笔记排序
可以根据创建时间、修改时间和颜色进行排序

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Createtimesort.jpg)

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Modifytimesort.jpg)

![Image text](https://raw.githubusercontent.com/cqm123456/NotePad/master/images/Colorsorting.jpg)
