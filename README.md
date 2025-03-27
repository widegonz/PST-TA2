```bash
bool Turtle::setPenCallback(const turtlesim::srv::SetPen::Request::SharedPtr req, turtlesim::srv::SetPen::Response::SharedPtr)
{
  pen_on_ = !req->off;
  if (req->off)
  {
    return true;
  }

  QPen pen(QColor(req->r, req->g, req->b));
  if (req->width != 0)
  {
    pen.setWidth(req->width);
  }

  pen_ = pen;
  return true;
}
````
