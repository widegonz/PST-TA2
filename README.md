```python
import rclpy
from rclpy.node import Node
from sensor_msgs.msg import LaserScan
from ackermann_msgs.msg import AckermannDriveStamped
import numpy as np

class GapFollower(Node):
    BUBBLE_RADIUS = 160  # in cm
    PREPROCESS_CONV_SIZE = 3
    BEST_POINT_CONV_SIZE = 80
    MAX_LIDAR_DIST = 6  # = 3m
    STRAIGHTS_SPEED = 6.0
    CORNERS_SPEED = 4.0
    STRAIGHTS_STEERING_ANGLE = np.pi / 18  # 10 degrees

    def __init__(self):
        super().__init__('gap_follower')
        self.radians_per_elem = None
        self.subscription = self.create_subscription(
            LaserScan,
            '/scan',
            self.lidar_callback,
            10
        )
        self.publisher = self.create_publisher(AckermannDriveStamped, '/drive', 10)

    def preprocess_lidar(self, ranges):
        """Preprocess the LiDAR scan array."""
        self.radians_per_elem = (2 * np.pi) / len(ranges)
        proc_ranges = np.array(ranges[0:-1])
        proc_ranges = np.convolve(proc_ranges, np.ones(self.PREPROCESS_CONV_SIZE), 'same') / self.PREPROCESS_CONV_SIZE
        proc_ranges = np.clip(proc_ranges, 0, self.MAX_LIDAR_DIST)
        return proc_ranges

    def find_max_gap(self, free_space_ranges):
        """Return the start index & end index of the max gap in free_space_ranges."""
        masked = np.ma.masked_where(free_space_ranges == 0, free_space_ranges)
        slices = np.ma.notmasked_contiguous(masked)
        max_len = slices[0].stop - slices[0].start
        chosen_slice = slices[0]
        for sl in slices[1:]:
            sl_len = sl.stop - sl.start
            if sl_len > max_len:
                max_len = sl_len
                chosen_slice = sl
        return chosen_slice.start, chosen_slice.stop

    def find_best_point(self, start_i, end_i, ranges):
        """Find the best point in the gap."""
        averaged_max_gap = np.convolve(ranges[start_i:end_i], np.ones(self.BEST_POINT_CONV_SIZE), 'same') / self.BEST_POINT_CONV_SIZE
        return averaged_max_gap.argmax() + start_i

    def get_angle(self, range_index, range_len):
        """Get the angle of a particular LiDAR element and transform it into a steering angle."""
        lidar_angle = (range_index - (range_len / 2)) * self.radians_per_elem
        steering_angle = lidar_angle / 2
        return steering_angle

    def lidar_callback(self, msg):
        """Process each LiDAR scan and publish an AckermannDriveStamped message."""
        proc_ranges = self.preprocess_lidar(msg.ranges)
        closest = proc_ranges.argmin()

         # Eliminar todos los puntos dentro del "bubble" (configurarlos a cero)
        min_index = int(closest - self.BUBBLE_RADIUS)
        max_index = int(closest + self.BUBBLE_RADIUS)
        if min_index < 0: min_index = 0
        if max_index > len(proc_ranges): max_index = len(proc_ranges) - 1
        proc_ranges[min_index:max_index] = 0

        gap_start, gap_end = self.find_max_gap(proc_ranges)
        best = self.find_best_point(gap_start, gap_end, proc_ranges)
        
        steering_angle = self.get_angle(best, len(proc_ranges))
        speed = self.CORNERS_SPEED if abs(steering_angle) > self.STRAIGHTS_STEERING_ANGLE else self.STRAIGHTS_SPEED
        
        drive_msg = AckermannDriveStamped()
        drive_msg.drive.speed = speed
        drive_msg.drive.steering_angle = steering_angle
        self.publisher.publish(drive_msg)


def main(args=None):
    rclpy.init(args=args)
    node = GapFollower()
    rclpy.spin(node)
    rclpy.shutdown()


if __name__ == '__main__':
    main()

```
