# Problem
While scrolling using a touchscreen the scroll latches to a single element for the duration of the scrolling which is from when
the user starts scrolling till the time that they lift their fingers; This behavior is known as scroll latching. Currently every wheel event is treated as an independent scrolling sequence and if not cancelled it scrolls the element that is under the
cursor. Implementing latching behavior for wheel scrolling makes it similar to touch and it is currently supported in
Firefox and Safari browsers.
Wheel scroll latching also enables a performance optimization which is not possible otherwise: With latching enabled only
the first wheel event of a scrolling sequence is cancellable, this improves performance in cases that there are blocking
wheel event listeners and the main thread is busy.

# Proposal
The proposal here is a summary of this [design doc](https://docs.google.com/document/d/1BizkQyW_FDU98sGxbHOZsAQWGYIA3xxXoFQZ3_XNo1o/edit#heading=h.1zmqj1pzgpl1):

To enable wheel scroll latching we need to know the beginning and end of a scrolling sequence. Some platforms (Mac and ChromeOS)
give us this information with either additional input events or extra data (phase information) in wheel events. On other
platforms a timer based solution is used to start a scrolling sequence on first wheel event and end it after not receiving any
wheel events during a timeout window.
DOM events during a scrolling sequence are sent to a single element and if not cancelled they will cause scrolling of the 
element or its first ansestor that can scroll in the given direction. To implement async wheel events only the first wheel of a
scroll sequence will be sent to JS before causing scroll and if not cancelled the rest of the wheel events are not cancellable
for the duration of the latched scroll sequence. The scroll handling is asynchronous with handling wheel events.
