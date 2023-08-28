```ahk
#Requires AutoHotkey v2.0

getAllWindow(){
    ids := WinGetList(,, "Program Manager")
    return ids
}

getWindowByTitle(title){
    allWin := getAllWindow()
    res := Array()

    for this_id in allWin
    {
        this_title := WinGetTitle(this_id)
        if(this_title == title) {
            res.Push(this_id)
        }
    }

    return res
}

getWindowContainTitle(title){
    allWin := getAllWindow()
    res := Array()

    for this_id in allWin
    {
        this_title := WinGetTitle(this_id)
        if(stringContainTarget(this_title, title)) {
            res.Push(this_id)
        }
    }

    return res
}

setWindowActive(aWinId){
    WinActivate aWinId
}


getWindowText(aWinId) {
    Text := WinGetText(aWinId)
    return Text
}

printWindowTitle(ids){
    for this_id in ids
    {
        this_title := WinGetTitle(this_id)
        MsgBox this_title
    }
}

stringContainTarget(s, target) {
    If InStr(s, target)
        return 1
    Else
        return 0
}

isWindowContainText(winId, text) {
    if(stringContainTarget(getWindowText(winId), text)) {
        return getWindowText(winId)
    }
    else {
        Controls := WinGetControlsHwnd(winId)
        for cid in Controls
        {
            if(StrLen(isWindowContainText(cid, text))) {
                return getWindowText(cid)
            }
        }

        return ""
    }
}

foundWindowUntil(title, waitSecMax, checkGapSec) {
    waitedSec :=0
    while(waitedSec < waitSecMax) {
        all := getWindowByTitle(title)
        if(all.Length) {
            return all
        }
        else {
            waitedSec := waitedSec + checkGapSec
            sleep checkGapSec*1000
        }
    }
    ;MsgBox Format( "Time out, not found: {1}", title)
    return []
}

sendKeysToWindow(winid, keys) {
    setWindowActive(winid)
    sleep 100
    SendInput  keys
}

showMessage(message, title, timeSec){
    MyGui := Gui(, title)
    MyGui.Opt("+AlwaysOnTop +Disabled -SysMenu +Owner -Caption")  ; +Owner avoids a taskbar button.
    MyGui.BackColor := "EEAA99"
    WinSetTransColor("EEAA99", MyGui)
    MyGui.SetFont("s20 q5")
    MyGui.Add("Text","cYellow w600", message)
    MyGui.Show("xCenter y0 NoActivate")  ; NoActivate avoids deactivating the currently active window.

    fn := closeWindowByTitle.Bind(title)
    SetTimer(fn, -timeSec*1000)
}

closeWindowByTitle(title) {
    winds := getWindowByTitle(title)
    for this_id in winds
    {
        this_title := WinGetTitle(this_id)
        if(this_title == title) {
            WinClose this_id
        }
    }
}

closeWindowContainTitle(title) {
    winds := getWindowContainTitle(title)
    for this_id in winds
    {
        WinClose this_id
    }
}

countDown(count) {
    countDownImpl(count, 1)
}

countDownImpl(count, first){
   if(!first) {
        prev := getWindowByTitle(Format( "the_Global_Count_Down_Window{1}", count+1))
        if(!prev.Length) {
            return
        }
        closeWindowContainTitle("the_Global_Count_Down_Window")
   }
   else {
        closeWindowContainTitle("the_Global_Count_Down_Window")
    }

    if(count < 0) {
        return
    }

    title := Format( "the_Global_Count_Down_Window{1}", count)
    MyGui := Gui(, title)
    MyGui.Opt("+AlwaysOnTop +Disabled -SysMenu +Owner -Caption")  ; +Owner avoids a taskbar button.
    MyGui.BackColor := "EEAA99"
    WinSetTransColor("EEAA99", MyGui)
    MyGui.SetFont("s20 q5")
    MyGui.Add("Text","cYellow w100", count)
    MyGui.Show("x0 y0 NoActivate")  ; NoActivate avoids deactivating the currently active window.

    fn := countDownImpl.Bind(count-1,0)
    SetTimer(fn, -970)
}

```
